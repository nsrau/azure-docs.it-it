---
title: I sistemi integrati di prendere decisioni di distribuzione connesse di Azure per Azure Stack | Microsoft Docs
description: Determinare le decisioni per le distribuzioni basate su Azure Stack di Azure a più nodi di pianificazione della distribuzione.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d64b834f1c6794976461c93d4ad1d05f8647e986
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414590"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>I sistemi integrati di Azure connessa di distribuzione di prendere decisioni per Azure Stack
Dopo aver deciso [modo in cui si integrerà Azure Stack nell'ambiente cloud ibrido](azure-stack-connection-models.md), è quindi possibile finalizzare le decisioni relative alla distribuzione di Azure Stack.

Distribuire Azure Stack, connesso ad Azure significa che è possibile avere Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS) per l'archivio identità. È anche possibile scegliere da una modello di fatturazione: come è a pagamento o basato sulla capacità. Una distribuzione connesso è l'opzione predefinita in quanto consente ai clienti di ottenere il massimo valore dai Azure Stack, in particolare per scenari di cloud ibrido che prevedono di Azure e Azure Stack. 

## <a name="choose-an-identity-store"></a>Scegliere un archivio identità
Con una distribuzione connessa, è possibile scegliere tra Azure Active Directory o AD FS per l'archivio identità. Una distribuzione disconnessa, senza connettività internet, possa solo usare AD FS.

La scelta di archivio identità non è rilevante per le macchine virtuali tenant (VM). Le macchine virtuali tenant possono scegliere il negozio di identità desiderato per la connessione a, a seconda del modo in cui verranno configurati: Azure AD, Windows Server Active Directory aggiunti a un dominio, gruppo di lavoro e così via. Ciò non è correlata alla decisione di provider di identità di Azure Stack. 

Ad esempio, se si distribuisce macchine virtuali in Azure Stack IaaS tenant e dovranno partecipare a un dominio Active Directory aziendale e usare gli account da lì, è possibile comunque eseguire questa operazione. Non è necessario usare l'archivio identità di Azure AD selezionato qui per tali account.

### <a name="azure-ad-identity-store"></a>Archivio di identità Azure AD
Quando si usa Azure AD per l'archivio identità richiede due account di Azure AD: un account amministratore globale e un account di fatturazione. Questi account possono essere l'account stesso, o account diversi. Usando lo stesso account utente potrebbe essere più semplice e utile se si dispone di un numero limitato di account di Azure, potrebbero suggerire le esigenze aziendali usando i due account:

1. **Account di amministratore globale** (obbligatorio solo per le distribuzioni connesse). Si tratta di un account di Azure che consente di creare applicazioni ed entità servizio per servizi di infrastruttura di Azure Stack in Azure Active Directory. Questo account deve avere autorizzazioni di amministratore di directory alla directory in cui verrà distribuito il sistema Azure Stack. Diventerà l'operatore"cloud" amministratore globale per Azure AD del tenant e verrà utilizzato: 
    - Per eseguire il provisioning e il delegato applicazioni ed entità servizio per tutti i servizi di Azure Stack che devono interagire con Azure Active Directory e l'API Graph. 
    - Come l'account di amministratore del servizio. Questo è il proprietario della sottoscrizione del provider predefinito (che non è possibile modificarla). È possibile accedere al portale di amministrazione di Azure Stack con questo account e usarlo per creare offerte e piani e impostare le quote di eseguire altre funzioni amministrative in Azure Stack.
2. **Account di fatturazione** (obbligatorio per entrambe connesse e disconnesse le distribuzioni). Questo account Azure viene usato per stabilire la relazione tra il sistema integrato Azure Stack e il back-end di Azure e-commerce di fatturazione. Si tratta dell'account che verrà addebitato con tariffe di Azure Stack. Questo account verrà essere utilizzato anche per offrire elementi nel marketplace e altri scenari ibridi. 

### <a name="ad-fs-identity-store"></a>Archivio di identità AD FS
Scegliere questa opzione se si vuole usare il proprio archivio di identità, ad esempio Active Directory aziendali, per gli account di amministratore del servizio.  

## <a name="choose-a-billing-model"></a>Scegliere un modello di fatturazione
È possibile scegliere **come è a pagamento** o nella **capacità** modello di fatturazione. Come si-pagamento fatturazione le distribuzioni di modelli devono essere in grado di uso dei report tramite una connessione ad Azure ad almeno una volta ogni 30 giorni. Pertanto, il modello di fatturazione di come è a pagamento è disponibile solo per le distribuzioni connesso.  

### <a name="pay-as-you-use"></a>Come si-pagamento
Con il modello di fatturazione di come è a pagamento, utilizzo viene addebitato a una sottoscrizione di Azure. Si paga solo quando si usano i servizi di Azure Stack. Se si tratta del modello si stabiliscono, sono necessari una sottoscrizione di Azure e l'ID dell'account associato all'abbonamento (ad esempio, serviceadmin@contoso.onmicrosoft.com). Le sottoscrizioni EA, CSP e CSL sono supportate. Report sull'utilizzo viene configurata durante [registrazione di Azure Stack](azure-stack-registration.md).

> [!NOTE]
> Nella maggior parte dei casi, i clienti aziendali useranno le sottoscrizioni EA e i provider di servizi userà le sottoscrizioni CSP o CSL.

Se si intende usare una sottoscrizione di CSP, consultare la tabella seguente per identificare la sottoscrizione di CSP da utilizzare, come l'approccio corretto varia a seconda dello scenario CSP:

|Scenario|Opzioni di sottoscrizione e di dominio|
|-----|-----|
|Si è un **Partner CSP diretto** o un' **Provider CSP indiretti**, e si opererà Azure Stack|Usare una sottoscrizione CSL (livello di servizio comune).<br>     oppure<br>Creare un tenant di Azure AD con un nome descrittivo in Partner Center. Ad esempio &lt;dell'organizzazione > CSPAdmin con una sottoscrizione di Azure CSP associata.|
|Si è un **rivenditore CSP indiretti**, e si opererà Azure Stack|Chiedere al Provider CSP indiretti per creare un tenant di Azure AD per l'organizzazione con una sottoscrizione di Azure CSP associata tramite Centro per i Partner.|

### <a name="capacity-based-billing"></a>Fatturazione in base della capacità
Se si decide di usare il modello di fatturazione di capacità, è necessario acquistare un Stack capacità piano SKU di Azure in base alla capacità del sistema. È necessario conoscere il numero di core fisici in Azure Stack per acquistare la quantità corretta. 

La fatturazione della capacità è necessario un Enterprise Agreement (EA) sottoscrizione di Azure per la registrazione. Il motivo è che registrazione i set di disponibilità dei prodotti in Marketplace, che richiede una sottoscrizione di Azure. La sottoscrizione non viene utilizzata per l'utilizzo di Azure Stack.

## <a name="learn-more"></a>Altre informazioni
- Per informazioni sui casi d'uso, acquisto, partner e fornitori di hardware OEM, vedere la [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) pagina del prodotto.
- Per informazioni sulla Guida di orientamento e la disponibilità geografica per Azure Stack i sistemi integrati, vedere il white paper: [Azure Stack: un'estensione di Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Per altre informazioni sulla creazione di pacchetti e prezzi di Microsoft Azure Stack [Scarica il PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Passaggi successivi
[Integrazione della rete datacenter](azure-stack-network.md)