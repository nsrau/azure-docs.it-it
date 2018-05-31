---
title: Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 07957d5ec843c414813d69b7084915bcd70a5a61
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930858"
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati possono accedere alle risorse. Ad esempio, è possibile limitare l'accesso a determinate risorse a dispositivi gestiti. Un criterio di accesso condizionale che richiede un dispositivo gestito è noto anche come criterio di accesso condizionale basato su dispositivo.

Questo argomento spiega in che modo è possibile configurare i criteri di accesso condizionale basato su dispositivo per le applicazioni connesse ad Azure AD. 


## <a name="before-you-begin"></a>Prima di iniziare

Collegamenti di accesso condizionale basato su dispositivo **Accesso condizionale ad Azure AD** e **Gestione dei dispositivi AD Azure insieme**. Se non si ha ancora familiarità con una di queste aree, è consigliabile leggere innanzitutto gli argomenti seguenti:

- **[Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)**: questo argomento offre una panoramica concettuale di accesso condizionale e la terminologia correlata.

- **[Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)**: questo argomento offre una panoramica delle diverse opzioni a disposizione per connettere i dispositivi con Azure AD. 



## <a name="managed-devices"></a>Dispositivi gestiti  

In un mondo in cui i dispositivi mobili e il cloud hanno sempre più importanza, Azure Active Directory consente ovunque l'accesso Single Sign-On a dispositivi, app e servizi. Per alcune risorse dell'ambiente, la concessione dell'accesso agli utenti appropriati potrebbe non essere sufficiente. Oltre che dagli utenti appropriati, potrebbe anche essere necessario fare in modo che i tentativi di accesso vengano eseguiti solo usando un dispositivo gestito.

Un dispositivo gestito è un dispositivo che soddisfa gli standard di sicurezza e conformità. In poche parole, i dispositivi gestiti sono dispositivi che ricadono sotto *qualche tipo* di controllo aziendale. In Azure AD il prerequisito per un dispositivo gestito è che sia stato registrato con Azure AD. La registrazione di un dispositivo crea un'identità per il dispositivo sotto forma di oggetto dispositivo. Questo oggetto viene usato da Azure per tenere traccia delle informazioni sullo stato relative a un dispositivo. Gli amministratori di Azure AD possono già usare questo oggetto per alternare (abilitare/disabilitare) lo stato di un dispositivo.
  
![Condizioni basate sul dispositivo](./media/active-directory-conditional-access-policy-connected-applications/32.png)

Per registrare un dispositivo con Azure AD, sono disponibili tre opzioni:

- **[Dispositivi registrati in Azure AD](device-management-introduction.md#azure-ad-registered-devices)**: per registrare un dispositivo personale con Azure AD

- **[Dispositivi aggiunti ad Azure AD](device-management-introduction.md#azure-ad-joined-devices)**: per registrare con Azure AD un dispositivo Windows 10 aziendale non aggiunto a un'istanza di AD locale. 

- **[Dispositivi aggiunti ad Azure AD ibrido](device-management-introduction.md#hybrid-azure-ad-joined-devices)**: per registrare con Azure AD un dispositivo Windows 10 aggiunto a un'istanza di AD locale.

Per diventare un dispositivo gestito, un dispositivo registrato può essere un dispositivo aggiunto ad Azure AD ibrido o un dispositivo che è stato contrassegnato come conforme.  

![Condizioni basate sul dispositivo](./media/active-directory-conditional-access-policy-connected-applications/47.png)


 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Richiedere i dispositivi aggiunti ad Azure AD ibrido

Nei criteri di accesso condizionale è possibile selezionare **Richiedi dispositivo aggiunto ad Azure AD ibrido** per fare in modo che le app cloud selezionate siano accessibili solo con un dispositivo gestito. 

![Condizioni basate sul dispositivo](./media/active-directory-conditional-access-policy-connected-applications/10.png)

Questa impostazione si applica solo ai dispositivi Windows 10 aggiunti a un'istanza di Azure AD locale. È possibile registrare questi dispositivi con Azure AD solo usando un join di Azure AD ibrido, un [processo automatizzato](device-management-hybrid-azuread-joined-devices-setup.md) per registrare un dispositivo Windows 10. 

![Condizioni basate sul dispositivo](./media/active-directory-conditional-access-policy-connected-applications/45.png)

In che modo un dispositivo aggiunto ad Azure AD ibrido diventa un dispositivo gestito?  Per i dispositivi aggiunti a un'istanza di AD locale, si presume che il controllo su questi dispositivi venga applicato usando soluzioni di gestione, ad esempio **System Center Configuration Manager (SCCM)** o i **criteri di gruppo**, per gestirli. Poiché non esiste alcun metodo che consenta ad Azure AD di determinare se uno di questi metodi è stato applicato a un dispositivo, il requisito che prevede un dispositivo aggiunto ad Azure AD ibrido è un meccanismo relativamente debole per richiedere un dispositivo gestito. È responsabilità dell'amministratore valutare se i metodi applicati ai dispositivi aggiunti al dominio locale siano abbastanza affidabili per costituire un dispositivo gestito se tale dispositivo è anche un dispositivo aggiunto ad Azure AD ibrido.


## <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

L'opzione per *richiedere che un dispositivo sia contrassegnato come conforme* è il modo più sicuro per richiedere un dispositivo gestito.

![Condizioni basate sul dispositivo](./media/active-directory-conditional-access-policy-connected-applications/11.png)

Questa opzione richiede che un dispositivo venga registrato con Azure AD e anche che venga contrassegnato come conforme da:
         
- Intune 
- Un sistema gestito di dispositivi mobili di terze parti che gestisce i dispositivi Windows 10 tramite l'integrazione di Azure AD 
 
![Condizioni basate sul dispositivo](./media/active-directory-conditional-access-policy-connected-applications/46.png)



Per un dispositivo contrassegnato come conforme, è possibile presupporre che: 

- I dispositivi mobili usati dalla forza lavoro per accedere ai dati aziendali sono gestiti
- Le app per dispositivi mobili usate dalla forza lavoro sono gestite
- Le informazioni della società sono protette grazie alla possibilità di controllare il modo in cui la forza lavoro vi accede e le condivide
- Il dispositivo e le relative app sono conformi ai requisiti di sicurezza aziendali




## <a name="next-steps"></a>Passaggi successivi

Prima di configurare un criterio di accesso condizionale basato su dispositivo nell'ambiente in uso, è necessario esaminare [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md).

