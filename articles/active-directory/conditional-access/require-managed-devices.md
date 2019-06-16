---
title: La modalità in - richiedono gestiti i dispositivi per accedere all'app cloud con Azure Active Directory l'accesso condizionale | Microsoft Docs
description: Informazioni su come configurare Azure Active Directory (Azure AD) basato su dispositivo criteri di accesso condizionale che richiedono i dispositivi gestiti per accedere all'app cloud.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 459dd981b73ae840b3fc61bd0cc83ecefb1cf393
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112153"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Procedura: Richiedi che i dispositivi gestiti per accedere all'app cloud con l'accesso condizionale

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory (Azure AD) consente ovunque l'accesso Single Sign-On ad app e servizi. Gli utenti autorizzati possono accedere alle app cloud da una vasta gamma di dispositivi, inclusi i dispositivi mobili e quelli personali. In numerosi ambienti, tuttavia, almeno alcune app devono essere accessibili solo da dispositivi che soddisfano determinati standard di sicurezza e conformità. I dispositivi di questo tipo sono noti anche come dispositivi gestiti. 

Questo articolo illustra come configurare i criteri di accesso condizionale che richiedono i dispositivi gestiti per accedere a determinate App cloud nell'ambiente in uso. 


## <a name="prerequisites"></a>Prerequisiti

Richiedere i dispositivi gestiti per i valori equivalenti di accesso di cloud app **accesso condizionale di Azure AD** e **gestione dei dispositivi AD Azure** tra loro. Se non si ha ancora familiarità con una di queste aree, è consigliabile leggere innanzitutto gli argomenti seguenti:

- **[Accesso condizionale in Azure Active Directory](../active-directory-conditional-access-azure-portal.md)**  -questo articolo offre una panoramica concettuale di accesso condizionale e la terminologia correlata.

- **[Introduzione alla gestione dei dispositivi in Azure Active Directory](../devices/overview.md)** : questo articolo offre una panoramica delle diverse opzioni che un'organizzazione ha a disposizione per tenere sotto controllo i dispositivi. 


## <a name="scenario-description"></a>Descrizione dello scenario

Trovare il giusto equilibrio tra sicurezza e produttività è una vera e propria sfida. L'ampia diffusione dei dispositivi supportati per l'accesso alle risorse cloud offre notevoli vantaggi in termini di produttività degli utenti. In alcuni casi, tuttavia, può essere necessario evitare che alcune risorse dell'ambiente risultino accessibili a dispositivi con un livello di protezione sconosciuto. Per le risorse di questo tipo è opportuno definire un requisito di accessibilità in modo che gli utenti possano accedervi solo tramite un dispositivo gestito. 

Grazie all'accesso condizionale di Azure AD, è possibile soddisfare questo requisito con un singolo criterio che concede l'accesso:

- Alle app cloud selezionato

- Per utenti e gruppi selezionati

- Richiesta di un dispositivo gestito


## <a name="managed-devices"></a>Dispositivi gestiti  

In poche parole, i dispositivi gestiti sono dispositivi che ricadono sotto *qualche tipo* di controllo aziendale. In Azure AD il prerequisito per un dispositivo gestito è che sia stato registrato con Azure AD. La registrazione di un dispositivo crea un'identità per il dispositivo sotto forma di oggetto dispositivo. Questo oggetto viene usato da Azure per tenere traccia delle informazioni sullo stato relative a un dispositivo. Gli amministratori di Azure AD possono già usare questo oggetto per alternare (abilitare/disabilitare) lo stato di un dispositivo.
  
![Condizioni basate sul dispositivo](./media/require-managed-devices/32.png)

Per registrare un dispositivo con Azure AD, sono disponibili tre opzioni:

- **[Dispositivi registrati in Azure AD](../devices/overview.md#azure-ad-registered-devices)** : per registrare un dispositivo personale con Azure AD

- **[Dispositivi aggiunti ad Azure AD](../devices/overview.md#azure-ad-joined-devices)** : per registrare con Azure AD un dispositivo Windows 10 aziendale non aggiunto a un'istanza di AD locale. 

- **[Dispositivi aggiunti ad Azure AD ibrido](../devices/overview.md#hybrid-azure-ad-joined-devices)** : per registrare con Azure AD un dispositivo Windows 10 o un dispositivo di livello inferiore supportato aggiunto a un'istanza di AD locale.

Per risultare gestito, un dispositivo registrato deve essere un **dispositivo aggiunto ad Azure AD ibrido** o un **dispositivo contrassegnato come conforme**.  

![Condizioni basate sul dispositivo](./media/require-managed-devices/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Richiedere i dispositivi aggiunti ad Azure AD ibrido

Nel criterio di accesso condizionale, è possibile selezionare **Richiedi dispositivo aggiunto ad Azure AD ibrido** per indicare che le app cloud selezionate sono accessibile solo tramite un dispositivo gestito. 

![Condizioni basate sul dispositivo](./media/require-managed-devices/10.png)

Questa impostazione si applica solo ai dispositivi Windows 10 o ai dispositivi di livello inferiore, come Windows 7 o Windows 8, aggiunti a un'istanza di Azure AD locale. È possibile registrare questi dispositivi con Azure AD solo usando un join di Azure AD ibrido, un [processo automatizzato](../devices/hybrid-azuread-join-plan.md) per registrare un dispositivo Windows 10. 

![Condizioni basate sul dispositivo](./media/require-managed-devices/45.png)

In che modo un dispositivo aggiunto ad Azure AD ibrido diventa un dispositivo gestito?  Per i dispositivi aggiunti a un'istanza di AD locale, si presume che il controllo su questi dispositivi venga applicato usando soluzioni di gestione, ad esempio **System Center Configuration Manager (SCCM)** o i **criteri di gruppo**, per gestirli. Poiché non esiste alcun metodo che consenta ad Azure AD di determinare se uno di questi metodi è stato applicato a un dispositivo, il requisito che prevede un dispositivo aggiunto ad Azure AD ibrido è un meccanismo relativamente debole per richiedere un dispositivo gestito. È responsabilità dell'amministratore valutare se i metodi applicati ai dispositivi aggiunti al dominio locale siano abbastanza affidabili per costituire un dispositivo gestito se tale dispositivo è anche un dispositivo aggiunto ad Azure AD ibrido.


## <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

L'opzione per *richiedere che un dispositivo sia contrassegnato come conforme* è il modo più sicuro per richiedere un dispositivo gestito.

![Condizioni basate sul dispositivo](./media/require-managed-devices/11.png)

Questa opzione richiede che un dispositivo venga registrato con Azure AD e anche che venga contrassegnato come conforme da:
         
- Intune.
- Un sistema di gestione dei dispositivi mobili (MDM) di terze parti che gestisce dispositivi Windows 10 tramite l'integrazione di Azure AD. I sistemi MDM di terze parti per sistemi operativi per dispositivo diversi da Windows 10 non sono supportati.
 
![Condizioni basate sul dispositivo](./media/require-managed-devices/46.png)



Per un dispositivo contrassegnato come conforme, è possibile presupporre che: 

- I dispositivi mobili usati dalla forza lavoro per accedere ai dati aziendali sono gestiti
- Le app per dispositivi mobili usate dalla forza lavoro sono gestite
- Le informazioni della società sono protette grazie alla possibilità di controllare il modo in cui la forza lavoro vi accede e le condivide
- Il dispositivo e le relative app sono conformi ai requisiti di sicurezza aziendali




## <a name="next-steps"></a>Passaggi successivi

Prima di configurare un criterio di accesso condizionale basato su dispositivo nell'ambiente in uso, è consigliabile esaminare i [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).

