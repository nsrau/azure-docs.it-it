---
title: "Azure AD Connect: Risolvere i problemi di ancoraggio di origine durante l'installazione | Microsoft Docs"
description: In questo argomento vengono illustrate le procedure risolvere i problemi con il punto di ancoraggio di origine durante l'installazione.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014242"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Risoluzione dei problemi di ancoraggio di origine durante l'installazione
Questo articolo illustra i diversi problemi che possono verificarsi durante l'installazione e offre modi per risolvere i problemi relativi alla ancoraggio di origine.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ancoraggio di origine non è valido in Azure Active Directory

### <a name="custom-installation"></a>Installazione personalizzata

Durante l'installazione personalizzata, Azure AD Connect legge il criterio di ancoraggio di origine da Azure Active Directory. Se il criterio esiste in Azure Active Directory, Azure AD Connect applica lo stesso criterio a meno che non venga sottoposto a override da parte del cliente. La procedura guidata informa l'utente è stato letto l'attributo. Inoltre, la procedura guidata genera un avviso se si prova a eseguire l'override di criteri di ancoraggio di origine.

Durante questa operazione di lettura, è possibile che il criterio di ancoraggio di origine in Azure Active Directory non è previsto. In questo caso, Azure AD Connect non sa quali l'ancoraggio di origine da usare e deve eseguire l'override manuale.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Per risolvere questo problema, è possibile sostituire manualmente l'ancoraggio di origine selezionando un attributo specifico. Procedere con questa opzione se e solo se si è certi di quale attributo da selezionare. Se non si è certi, rivolgersi [supporto tecnico Microsoft](https://support.microsoft.com/contactus/) per materiale sussidiario. Se si modifica il criterio di ancoraggio di origine, è possibile interrompere l'associazione tra gli utenti locali e le relative risorse di Azure associate.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Installazione rapida
Durante l'installazione rapida, Azure AD Connect legge il criterio di ancoraggio di origine da Azure Active Directory. Se il criterio esiste in Azure Active Directory, Azure AD Connect applica gli stessi criteri. Non è possibile eseguire l'override manuale.

Durante questa operazione di lettura, è possibile che il criterio di ancoraggio di origine in Azure Active Directory non è previsto. In questo caso, Azure AD Connect non sa quale deve essere l'ancoraggio di origine.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Per risolvere questo problema, è necessario installare nuovamente utilizzando la modalità personalizzata ed eseguire l'override manualmente l'ancoraggio di origine selezionando un attributo specifico. Procedere con questa opzione se e solo se si è certi di quale attributo da selezionare. Se non si è certi, rivolgersi [supporto tecnico Microsoft](https://support.microsoft.com/contactus/) per materiale sussidiario. Se si modifica il criterio di ancoraggio di origine, è possibile interrompere l'associazione tra gli utenti locali e le relative risorse di Azure associate.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ancoraggio di origine non valida nel motore di sincronizzazione
Durante l'installazione, è possibile Azure AD Connect tenta di configurare il motore di sincronizzazione utilizzando un ancoraggio di origine non è valido. Questa operazione è molto probabilmente un problema del prodotto e l'installazione di Azure AD Connect avrà esito negativo. Contattare [supporto tecnico Microsoft](https://support.microsoft.com/contactus/) se si esegue in a questo problema.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).