---
title: "Azure AD Connect: Risolvere i problemi di ancoraggio di origine durante l'installazione Documenti Microsoft"
description: In questo argomento vengono illustrate le procedure per la risoluzione dei problemi relativi all'ancoraggio di origine durante l'installazione.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114156"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Risoluzione dei problemi relativi all'ancoraggio di origine durante l'installazione
In questo articolo vengono illustrati i diversi problemi correlati all'ancoraggio di origine che possono verificarsi durante l'installazione e vengono fornite informazioni su come risolvere questi problemi.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ancoraggio di origine non valido in Azure Active Directory

### <a name="custom-installation"></a>Installazione personalizzata

Durante l'installazione personalizzata, Azure AD Connect legge i criteri di ancoraggio di origine da Azure Active Directory.During custom installation, Azure AD Connect reads the source anchor policy from Azure Active Directory. Se il criterio esiste in Azure Active Directory, Azure AD Connect applica gli stessi criteri a meno che non venga sostituito dal cliente. La procedura guidata informa l'utente quale attributo è stato letto. Inoltre, la procedura guidata avvisa se si tenta di eseguire l'override del criterio di ancoraggio di origine.

Durante questa operazione di lettura, è possibile che i criteri di ancoraggio di origine in Azure Active Directory non sia imprevisto. In questo caso, Azure AD Connect non sa quale sia l'ancoraggio di origine da usare e richiede l'override manuale.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Per risolvere questo problema, è possibile sostituire manualmente l'ancoraggio di origine selezionando un attributo specifico. Procedere con questa opzione se e solo se si è certi di quale attributo selezionare. Se non si è certi, contattare [il supporto tecnico Microsoft](https://support.microsoft.com/contactus/) per istruzioni. Se si modificano i criteri di ancoraggio di origine, è possibile che interrompano l'associazione tra gli utenti locali e le risorse di Azure associate.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Installazione rapida
Durante l'installazione rapida, Azure AD Connect legge i criteri di ancoraggio di origine da Azure Active Directory.During express installation, Azure AD Connect reads the source anchor policy from Azure Active Directory. Se il criterio esiste in Azure Active Directory, Azure AD Connect applica gli stessi criteri. Non è disponibile alcuna opzione per eseguire la sostituzione manuale.

Durante questa operazione di lettura, è possibile che i criteri di ancoraggio di origine in Azure Active Directory non sia imprevisto. In questo caso, Azure AD Connect non sa quale dovrebbe essere l'ancoraggio di origine.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Per risolvere questo problema, è necessario reinstallare utilizzando la modalità personalizzata e sostituire manualmente l'ancoraggio di origine selezionando un attributo specifico. Procedere con questa opzione se e solo se si è certi di quale attributo selezionare. Se non si è certi, contattare [il supporto tecnico Microsoft](https://support.microsoft.com/contactus/) per istruzioni. Se si modificano i criteri di ancoraggio di origine, è possibile che interrompano l'associazione tra gli utenti locali e le risorse di Azure associate.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ancoraggio di origine non valido nel motore di sincronizzazioneInvalid Source Anchor in Sync Engine
Durante l'installazione, è possibile che Azure AD Connect tenti di configurare il motore di sincronizzazione usando un ancoraggio di origine non valido. Questa operazione è molto probabilmente un problema del prodotto e l'installazione di Azure AD Connect avrà esito negativo. Se si esegue questo problema, contattare il [supporto tecnico Microsoft.](https://support.microsoft.com/contactus/)</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).