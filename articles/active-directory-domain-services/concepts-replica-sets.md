---
title: La replica imposta i concetti per Azure AD Domain Services | Microsoft Docs
description: Informazioni sui set di repliche in Azure Active Directory Domain Services e sul modo in cui forniscono la ridondanza per le applicazioni che richiedono servizi di identità.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: joflore
ms.openlocfilehash: 499f4df303993d97ebb4eb38de98828b085aff00
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961069"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services-preview"></a>La replica imposta i concetti e le funzionalità per Azure Active Directory Domain Services (anteprima)

Quando si crea un dominio gestito Azure Active Directory Domain Services (Azure AD DS), si definisce uno spazio dei nomi univoco. Questo spazio dei nomi è il nome di dominio, ad esempio *aaddscontoso.com* e quindi nell'area di Azure selezionata vengono distribuiti due controller di dominio. Questa distribuzione di controller di dominio è nota come set di repliche.

È possibile espandere un dominio gestito in modo che ogni tenant di Azure AD contenga più di un set di repliche. È possibile aggiungere i set di repliche a qualsiasi rete virtuale con peering in qualsiasi area di Azure che supporti Azure AD DS. I set di replica aggiuntivi in aree di Azure diverse forniscono il ripristino di emergenza geografico per le applicazioni legacy se un'area di Azure passa offline.

I set di repliche sono attualmente disponibili in anteprima.

> [!NOTE]
> I set di repliche non consentono di distribuire più domini gestiti univoci in un singolo tenant di Azure. Ogni set di repliche contiene gli stessi dati.

## <a name="how-replica-sets-work"></a>Funzionamento del set di repliche

Quando si crea un dominio gestito, ad esempio *aaddscontoso.com*, viene creato un set di repliche iniziale. I set di repliche aggiuntivi condividono lo stesso spazio dei nomi e la stessa configurazione. Le modifiche apportate ad Azure AD DS, ad esempio alla configurazione, a identità utente e credenziali, ai gruppi, agli oggetti Criteri di gruppo e agli oggetti computer e le modifiche di altro tipo, vengono applicate a tutti i set di repliche nel dominio gestito con la replica di AD DS.

Ogni set di repliche viene creato in una rete virtuale. Ogni rete virtuale deve essere sottoposta a peering in tutte le altre reti virtuali che ospitano il set di repliche di un dominio gestito. Questa configurazione crea una topologia di rete mesh che supporta la replica della directory. Una rete virtuale può supportare più set di repliche, purché ogni set di repliche si trovi in un'altra subnet virtuale.

Tutti i set di repliche vengono inseriti nello stesso sito Active Directory. Di conseguenza, tutte le modifiche vengono propagate usando la replica all'insito per la convergenza rapida.

> [!NOTE]
> Non è possibile definire siti distinti e definire le impostazioni di replica tra set di repliche.

Il diagramma seguente mostra un dominio gestito con due set di repliche. Il primo set di repliche viene creato con lo spazio dei nomi del dominio. Successivamente, viene creato un secondo set di repliche:

![Diagramma del dominio gestito di esempio con due set di repliche](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> I set di repliche garantiscono la disponibilità dei servizi di autenticazione nelle aree in cui è configurato un set di repliche. Per un'applicazione con ridondanza geografica se si verifica un'interruzione a livello di area, anche la piattaforma applicativa che si basa sul dominio gestito deve risiedere nell'altra area.
>
> La resilienza di altri servizi necessari per il funzionamento dell'applicazione, ad esempio macchine virtuali di Azure o servizi app Azure, non viene fornita dai set di repliche. La progettazione della disponibilità di altri componenti dell'applicazione deve prendere in considerazione le funzionalità di resilienza per i servizi che costituiscono l'applicazione.

Nell'esempio seguente viene illustrato un dominio gestito con tre set di repliche per fornire ulteriormente la resilienza e garantire la disponibilità dei servizi di autenticazione. In entrambi gli esempi, i carichi di lavoro dell'applicazione si trovano nella stessa area del set di repliche di dominio gestito:

![Diagramma del dominio gestito di esempio con tre set di repliche](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

Lo SKU predefinito per un dominio gestito è lo SKU *Enterprise* , che supporta più set di repliche. Per creare set di repliche aggiuntivi se è stato modificato lo SKU *standard* , [aggiornare il dominio gestito](change-sku.md) a *Enterprise* o *Premium*.

Il numero massimo di set di repliche supportati durante l'anteprima è quattro, inclusa la prima replica creata al momento della creazione del dominio gestito.

La fatturazione per ogni set di repliche è basata sullo SKU di configurazione del dominio. Se, ad esempio, si dispone di un dominio gestito che usa lo SKU *Enterprise* e si hanno tre set di repliche, la sottoscrizione verrà fatturata ogni ora per ognuno dei tre set di repliche.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="can-i-use-my-production-managed-domain-with-this-preview"></a>È possibile usare il dominio gestito di produzione con questa anteprima?

I set di repliche sono una funzionalità in anteprima pubblica di Azure AD Domain Services. È possibile usare un dominio gestito di produzione, ma tenere presente le differenze di supporto esistenti per le funzionalità ancora in anteprima. Per altre informazioni sulle anteprime, vedere il [contratto di servizio per le anteprime di Azure Active Directory](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>È possibile creare un set di repliche in una sottoscrizione diversa dal dominio gestito?

No. I set di repliche devono trovarsi nella stessa sottoscrizione del dominio gestito.

### <a name="how-many-replica-sets-can-i-create"></a>Quanti set di repliche è possibile creare?

L'anteprima è limitata a un massimo di quattro set di repliche, ovvero il set di repliche iniziale per il dominio gestito, più tre set di repliche aggiuntive.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Come vengono sincronizzate le informazioni di utenti e gruppi con i set di repliche?

Tutti i set di repliche sono connessi tra loro usando un peering di rete virtuale mesh. Un set di repliche riceve gli aggiornamenti di utenti e gruppi da Azure AD. Tali modifiche vengono quindi replicate negli altri set di repliche tramite la replica di servizi di dominio Active Directory all'area tramite la rete con peering.

Analogamente a quanto avviene con servizi di dominio Active Directory in locale, uno stato disconnesso esteso può causare l'interruzione della replica. Poiché le reti virtuali con peering non sono transitive, i requisiti di progettazione per i set di repliche richiedono una topologia di rete completamente mesh.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>Ricerca per categorie apportare modifiche nel dominio gestito dopo aver impostato I set di repliche?

Le modifiche all'interno del dominio gestito funzionano esattamente come in precedenza. Si [Crea e si usa una macchina virtuale di gestione con gli strumenti di amministrazione remota del server aggiunti al dominio gestito](tutorial-create-management-vm.md). È possibile aggiungere al dominio gestito tutte le macchine virtuali di gestione desiderate.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i set di repliche, [creare e configurare un dominio gestito Azure AD DS][tutorial-create-advanced]. Al momento della distribuzione, [creare e usare set di repliche aggiuntivi][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
