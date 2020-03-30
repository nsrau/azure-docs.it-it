---
title: L'accesso condizionale richiede un dispositivo gestito - Azure Active DirectoryConditional Access require managed device - Azure Active Directory
description: Informazioni su come configurare i criteri di accesso condizionale basati su dispositivo di Azure Active Directory (Azure AD) che richiedono dispositivi gestiti per l'accesso alle app cloud.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481484"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Procedura: Richiedere dispositivi gestiti per l'accesso alle app cloud con accesso condizionaleHow To: Require managed devices for cloud app access with Conditional Access

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory (Azure AD) consente ovunque l'accesso Single Sign-On ad app e servizi. Gli utenti autorizzati possono accedere alle app cloud da una vasta gamma di dispositivi, inclusi i dispositivi mobili e quelli personali. In numerosi ambienti, tuttavia, almeno alcune app devono essere accessibili solo da dispositivi che soddisfano determinati standard di sicurezza e conformità. I dispositivi di questo tipo sono noti anche come dispositivi gestiti. 

Questo articolo illustra come configurare i criteri di accesso condizionale che richiedono ai dispositivi gestiti di accedere a determinate app cloud nell'ambiente. 

## <a name="prerequisites"></a>Prerequisiti

La richiesta di dispositivi gestiti per l'accesso alle app cloud lega **l'accesso condizionale** di Azure AD e la gestione dei dispositivi di Azure **AD** insieme. Se non si ha ancora familiarità con una di queste aree, è consigliabile leggere innanzitutto gli argomenti seguenti:

- **[Accesso condizionale in Azure Active Directory:](../active-directory-conditional-access-azure-portal.md)** in questo articolo viene fornita una panoramica concettuale dell'accesso condizionale e della terminologia correlata.
- **[Introduzione alla gestione dei dispositivi in Azure Active Directory](../devices/overview.md)**: questo articolo offre una panoramica delle diverse opzioni che un'organizzazione ha a disposizione per tenere sotto controllo i dispositivi. 
- Per il supporto di Chrome in **Windows 10 Creators Update (versione 1703)** o versione successiva, installa l'estensione [Account di Windows 10.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) Questa estensione è necessaria quando un criterio di accesso condizionale richiede dettagli specifici del dispositivo.

>[!NOTE] 
> È consigliabile usare i criteri di accesso condizionale basati sul dispositivo di Azure AD per ottenere l'imposizione migliore dopo l'autenticazione iniziale del dispositivo. Ciò include la chiusura delle sessioni se il dispositivo non è conforme e il flusso del codice del dispositivo.


## <a name="scenario-description"></a>Descrizione dello scenario

Trovare il giusto equilibrio tra sicurezza e produttività è una vera e propria sfida. L'ampia diffusione dei dispositivi supportati per l'accesso alle risorse cloud offre notevoli vantaggi in termini di produttività degli utenti. In alcuni casi, tuttavia, può essere necessario evitare che alcune risorse dell'ambiente risultino accessibili a dispositivi con un livello di protezione sconosciuto. Per le risorse di questo tipo è opportuno definire un requisito di accessibilità in modo che gli utenti possano accedervi solo tramite un dispositivo gestito. 

Con l'accesso condizionale di Azure AD è possibile soddisfare questo requisito con un singolo criterio che concede l'accesso:With Azure AD Conditional Access, you can address this requirement with a single policy that grants access:

- Alle app cloud selezionato
- Per utenti e gruppi selezionati
- Richiesta di un dispositivo gestito

## <a name="managed-devices"></a>Dispositivi gestiti  

In poche parole, i dispositivi gestiti sono dispositivi che ricadono sotto *qualche tipo* di controllo aziendale. In Azure AD il prerequisito per un dispositivo gestito è che sia stato registrato con Azure AD. La registrazione di un dispositivo crea un'identità per il dispositivo sotto forma di oggetto dispositivo. Questo oggetto viene usato da Azure per tenere traccia delle informazioni sullo stato relative a un dispositivo. Gli amministratori di Azure AD possono già usare questo oggetto per alternare (abilitare/disabilitare) lo stato di un dispositivo.
  
![Condizioni basate sul dispositivo](./media/require-managed-devices/32.png)

Per registrare un dispositivo con Azure AD, sono disponibili tre opzioni: 

- **Dispositivi registrati in Azure AD**: per registrare un dispositivo personale con Azure AD
- **Dispositivi aggiunti ad Azure AD**: per registrare con Azure AD un dispositivo Windows 10 aziendale non aggiunto a un'istanza di AD locale. 
- **Dispositivi aggiunti ad Azure AD ibrido**: per registrare con Azure AD un dispositivo Windows 10 o un dispositivo di livello inferiore supportato aggiunto a un'istanza di AD locale.

Queste tre opzioni sono descritte nell'articolo [Che cos'è un'identità del dispositivo?](../devices/overview.md)

Per risultare gestito, un dispositivo registrato deve essere un **dispositivo aggiunto ad Azure AD ibrido** o un **dispositivo contrassegnato come conforme**.  

![Condizioni basate sul dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Richiedere i dispositivi aggiunti ad Azure AD ibrido

Nei criteri di accesso condizionale è possibile selezionare **Richiedi dispositivo aggiunto ad Azure AD ibrido** per indicare che è possibile accedere alle app cloud selezionate solo tramite un dispositivo gestito. 

![Condizioni basate sul dispositivo](./media/require-managed-devices/10.png)

Questa impostazione si applica solo ai dispositivi Windows 10 o ai dispositivi di livello inferiore, come Windows 7 o Windows 8, aggiunti a un'istanza di Azure AD locale. È possibile registrare questi dispositivi con Azure AD solo usando un join di Azure AD ibrido, un [processo automatizzato](../devices/hybrid-azuread-join-plan.md) per registrare un dispositivo Windows 10. 

![Condizioni basate sul dispositivo](./media/require-managed-devices/45.png)

In che modo un dispositivo aggiunto ad Azure AD ibrido diventa un dispositivo gestito?  Per i dispositivi aggiunti a un'area di active Directory locale, si presuppone che il controllo su questi dispositivi venga applicato usando soluzioni di gestione come **Configuration Manager** o Criteri di **gruppo (GP)** per gestirli. Poiché non esiste alcun metodo che consenta ad Azure AD di determinare se uno di questi metodi è stato applicato a un dispositivo, il requisito che prevede un dispositivo aggiunto ad Azure AD ibrido è un meccanismo relativamente debole per richiedere un dispositivo gestito. È responsabilità dell'amministratore valutare se i metodi applicati ai dispositivi aggiunti al dominio locale siano abbastanza affidabili per costituire un dispositivo gestito se tale dispositivo è anche un dispositivo aggiunto ad Azure AD ibrido.

## <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

L'opzione per *richiedere che un dispositivo sia contrassegnato come conforme* è il modo più sicuro per richiedere un dispositivo gestito.

![Condizioni basate sul dispositivo](./media/require-managed-devices/11.png)

Questa opzione richiede che un dispositivo venga registrato con Azure AD e anche che venga contrassegnato come conforme da:
         
- Intune
- Un sistema di gestione dei dispositivi mobili (MDM) di terze parti che gestisce dispositivi Windows 10 tramite l'integrazione di Azure AD. I sistemi MDM di terze parti per sistemi operativi per dispositivo diversi da Windows 10 non sono supportati.
 
![Condizioni basate sul dispositivo](./media/require-managed-devices/46.png)

Per un dispositivo contrassegnato come conforme, è possibile presupporre che: 

- I dispositivi mobili usati dalla forza lavoro per accedere ai dati aziendali sono gestiti
- Le app per dispositivi mobili usate dalla forza lavoro sono gestite
- Le informazioni della società sono protette grazie alla possibilità di controllare il modo in cui la forza lavoro vi accede e le condivide
- Il dispositivo e le relative app sono conformi ai requisiti di sicurezza aziendali

### <a name="known-behavior"></a>Comportamento noto

In Windows 7, iOS, Android, macOS e alcuni browser Web di terze parti Azure AD identifica il dispositivo usando un certificato client di cui viene eseguito il provisioning quando il dispositivo viene registrato con Azure AD. Quando un utente accede per la prima volta tramite il browser, all'utente viene richiesto di selezionare il certificato. L'utente finale deve selezionare questo certificato prima di poter continuare a utilizzare il browser.

## <a name="next-steps"></a>Passaggi successivi

Prima di configurare criteri di accesso condizionale basati su dispositivo nell'ambiente, è consigliabile esaminare [le procedure consigliate per l'accesso condizionale in Azure Active Directory.](best-practices.md)
