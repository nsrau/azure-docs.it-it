---
title: "Azure AD Connect: risolvere i problemi di ancoraggio di origine durante l'installazione | Microsoft Docs"
description: In questo argomento viene descritta la procedura per risolvere i problemi relativi all'ancoraggio di origine durante l'installazione.
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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "62114156"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Risoluzione dei problemi di ancoraggio di origine durante l'installazione
Questo articolo illustra i diversi problemi correlati all'ancoraggio di origine che possono verificarsi durante l'installazione e offre modi per risolvere questi problemi.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ancoraggio di origine non valido in Azure Active Directory

### <a name="custom-installation"></a>Installazione personalizzata

Durante l'installazione personalizzata, Azure AD Connect legge i criteri di ancoraggio di origine da Azure Active Directory. Se il criterio è presente in Azure Active Directory, Azure AD Connect applica lo stesso criterio a meno che non venga sottoposto a override dal cliente. La procedura guidata informa l'utente dell'attributo letto. Inoltre, la procedura guidata avvisa se si tenta di eseguire l'override dei criteri di ancoraggio di origine.

Durante questa operazione di lettura, è possibile che i criteri di ancoraggio di origine in Azure Active Directory siano imprevisti. In questo caso, Azure AD Connect non conosce l'ancoraggio di origine da usare e richiede l'override manuale.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Per risolvere questo problema, è possibile sostituire manualmente l'ancoraggio di origine selezionando un attributo specifico. Procedere con questa opzione solo se si è certi dell'attributo da selezionare. In caso di dubbi, contattare il [supporto tecnico Microsoft](https://support.microsoft.com/contactus/) per informazioni aggiuntive. Se si modifica il criterio di ancoraggio di origine, l'associazione tra gli utenti locali e le risorse di Azure associate può essere interrotta.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Installazione rapida
Durante l'installazione rapida Azure AD Connect legge i criteri di ancoraggio di origine da Azure Active Directory. Se il criterio è presente in Azure Active Directory, Azure AD Connect applica gli stessi criteri. Non è possibile eseguire l'override manuale.

Durante questa operazione di lettura, è possibile che i criteri di ancoraggio di origine in Azure Active Directory siano imprevisti. In questo caso, Azure AD Connect non conosce l'ancoraggio di origine.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Per risolvere questo problema, è necessario reinstallare usando la modalità personalizzata e sostituire manualmente l'ancoraggio di origine selezionando un attributo specifico. Procedere con questa opzione solo se si è certi dell'attributo da selezionare. In caso di dubbi, contattare il [supporto tecnico Microsoft](https://support.microsoft.com/contactus/) per informazioni aggiuntive. Se si modifica il criterio di ancoraggio di origine, l'associazione tra gli utenti locali e le risorse di Azure associate può essere interrotta.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ancoraggio di origine non valido nel motore di sincronizzazione
Durante l'installazione, è possibile Azure AD Connect tenta di configurare il motore di sincronizzazione usando un ancoraggio di origine non valido. Questa operazione è probabilmente un problema del prodotto e l'installazione di Azure AD Connect avrà esito negativo. Se si esegue questo problema, contattare il [supporto tecnico Microsoft](https://support.microsoft.com/contactus/) .</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).