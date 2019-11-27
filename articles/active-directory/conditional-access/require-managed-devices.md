---
title: L'accesso condizionale richiede il Azure Active Directory del dispositivo gestito
description: Informazioni su come configurare i criteri di accesso condizionale basato su dispositivo Azure Active Directory (Azure AD) che richiedono i dispositivi gestiti per l'accesso alle app cloud.
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
ms.openlocfilehash: 0caf8e8d5e18efc0a7332f97acccc394051ed360
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452391"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Procedura: richiedere i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory (Azure AD) consente ovunque l'accesso Single Sign-On ad app e servizi. Gli utenti autorizzati possono accedere alle app cloud da una vasta gamma di dispositivi, inclusi i dispositivi mobili e quelli personali. In numerosi ambienti, tuttavia, almeno alcune app devono essere accessibili solo da dispositivi che soddisfano determinati standard di sicurezza e conformità. I dispositivi di questo tipo sono noti anche come dispositivi gestiti. 

Questo articolo illustra come configurare i criteri di accesso condizionale che richiedono ai dispositivi gestiti di accedere ad alcune app Cloud nell'ambiente in uso. 

## <a name="prerequisites"></a>prerequisiti

La richiesta di dispositivi gestiti per i vincoli di accesso alle app Cloud **Azure ad l'accesso condizionale** e la **gestione dei dispositivi Azure ad** insieme. Se non si ha ancora familiarità con una di queste aree, è consigliabile leggere innanzitutto gli argomenti seguenti:

- **[Accesso condizionale in Azure Active Directory: in](../active-directory-conditional-access-azure-portal.md)** questo articolo viene fornita una panoramica concettuale dell'accesso condizionale e della terminologia correlata.
- **[Introduzione alla gestione dei dispositivi in Azure Active Directory](../devices/overview.md)** : questo articolo offre una panoramica delle diverse opzioni che un'organizzazione ha a disposizione per tenere sotto controllo i dispositivi. 

## <a name="scenario-description"></a>Descrizione dello scenario

Trovare il giusto equilibrio tra sicurezza e produttività è una vera e propria sfida. L'ampia diffusione dei dispositivi supportati per l'accesso alle risorse cloud offre notevoli vantaggi in termini di produttività degli utenti. In alcuni casi, tuttavia, può essere necessario evitare che alcune risorse dell'ambiente risultino accessibili a dispositivi con un livello di protezione sconosciuto. Per le risorse di questo tipo è opportuno definire un requisito di accessibilità in modo che gli utenti possano accedervi solo tramite un dispositivo gestito. 

Con l'accesso condizionale Azure AD, è possibile soddisfare questo requisito con un singolo criterio che concede l'accesso:

- Alle app cloud selezionato
- Per utenti e gruppi selezionati
- Richiesta di un dispositivo gestito

## <a name="managed-devices"></a>Dispositivi gestiti  

In poche parole, i dispositivi gestiti sono dispositivi che ricadono sotto *qualche tipo* di controllo aziendale. In Azure AD il prerequisito per un dispositivo gestito è che sia stato registrato con Azure AD. La registrazione di un dispositivo crea un'identità per il dispositivo sotto forma di oggetto dispositivo. Questo oggetto viene usato da Azure per tenere traccia delle informazioni sullo stato relative a un dispositivo. Gli amministratori di Azure AD possono già usare questo oggetto per alternare (abilitare/disabilitare) lo stato di un dispositivo.
  
![Condizioni basate sul dispositivo](./media/require-managed-devices/32.png)

Per registrare un dispositivo con Azure AD, sono disponibili tre opzioni: 

- **Azure ad dispositivi registrati** : per ottenere un dispositivo personale registrato con Azure ad
- **Dispositivi Azure ad aggiunti** : per ottenere un dispositivo Windows 10 aziendale non aggiunto a un'istanza di ad locale registrata con Azure ad. 
- **Dispositivi Azure ad ibrido aggiunti** : per ottenere un dispositivo di livello inferiore Windows 10 o supportato aggiunto a un'istanza di ad locale registrata con Azure ad.

Queste tre opzioni sono illustrate nell'articolo [che cos'è un'identità del dispositivo?](../devices/overview.md)

Per risultare gestito, un dispositivo registrato deve essere un **dispositivo aggiunto ad Azure AD ibrido** o un **dispositivo contrassegnato come conforme**.  

![Condizioni basate sul dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Richiedere i dispositivi aggiunti ad Azure AD ibrido

Nel criterio di accesso condizionale è possibile selezionare **richiedi Azure ad ibrido dispositivo aggiunto** per indicare che è possibile accedere alle app Cloud selezionate solo usando un dispositivo gestito. 

![Condizioni basate sul dispositivo](./media/require-managed-devices/10.png)

Questa impostazione si applica solo ai dispositivi Windows 10 o ai dispositivi di livello inferiore, come Windows 7 o Windows 8, aggiunti a un'istanza di Azure AD locale. È possibile registrare questi dispositivi con Azure AD solo usando un join di Azure AD ibrido, un [processo automatizzato](../devices/hybrid-azuread-join-plan.md) per registrare un dispositivo Windows 10. 

![Condizioni basate sul dispositivo](./media/require-managed-devices/45.png)

In che modo un dispositivo aggiunto ad Azure AD ibrido diventa un dispositivo gestito?  Per i dispositivi aggiunti a un'istanza di AD locale, si presume che il controllo su questi dispositivi venga applicato usando soluzioni di gestione, ad esempio **System Center Configuration Manager (SCCM)** o i **criteri di gruppo**, per gestirli. Poiché non esiste alcun metodo che consenta ad Azure AD di determinare se uno di questi metodi è stato applicato a un dispositivo, il requisito che prevede un dispositivo aggiunto ad Azure AD ibrido è un meccanismo relativamente debole per richiedere un dispositivo gestito. È responsabilità dell'amministratore valutare se i metodi applicati ai dispositivi aggiunti al dominio locale siano abbastanza affidabili per costituire un dispositivo gestito se tale dispositivo è anche un dispositivo aggiunto ad Azure AD ibrido.

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

> [!NOTE]
> Se si configura un criterio per richiedere i dispositivi conformi, è possibile che venga richiesto agli utenti Mac, iOS e Android di selezionare un certificato del dispositivo durante la valutazione dei criteri. Si tratta di un comportamento noto.

## <a name="next-steps"></a>Passaggi successivi

Prima di configurare i criteri di accesso condizionale basato su dispositivo nell'ambiente in uso, è consigliabile esaminare le [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md).
