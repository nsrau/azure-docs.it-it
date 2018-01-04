---
title: Azure Active Directory strumento cosa accade se l'accesso condizionale - anteprima | Documenti Microsoft
description: Informazioni su come testare la configurazione dei criteri di accesso condizionale di Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/21/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: de6b3dcd77132154e583d7333983d6745c4aa3bd
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Azure Active Directory strumento cosa accade se l'accesso condizionale - anteprima

[Accesso condizionale](active-directory-conditional-access-azure-portal.md) è una funzionalità di Azure Active Directory (Azure AD) che consente di controllare la modalità di accesso agli utenti autorizzati App cloud. Come sapere cosa aspettarsi modulo i criteri di accesso condizionale nell'ambiente in uso? Per rispondere a questa domanda, è possibile utilizzare il **strumento cosa accade se l'accesso condizionale**.

In questo articolo viene illustrato come è possibile utilizzare questo strumento per testare i criteri di accesso condizionale.

## <a name="what-it-is"></a>Che cos'è

Il **accesso condizionale cosa fare se lo strumento Criteri di** consente di comprendere l'impatto dei criteri di accesso condizionale nell'ambiente. Anziché test determinano i criteri mediante l'esecuzione di più accessi manualmente, questo strumento consente di valutare un simulato Accedi di un utente. Simulazione di stima dell'impatto di questo Accedi con per i criteri e genera un report di simulazione. Il report non sono elencate solo condizionale applicato criteri di accesso, ma anche [criteri classici](active-directory-conditional-access-migration.md#classic-policies) se presenti.    

Indicare se tools fornisce anche un modo per rapidamente determinare i criteri che si applicano a un utente specifico. È possibile utilizzare le informazioni, ad esempio, se è necessario risolvere un problema.  

## <a name="how-it-works"></a>Funzionamento

Nel **strumento cosa accade se l'accesso condizionale**, è necessario innanzitutto configurare le impostazioni dello scenario per simulare Accedi. Queste impostazioni includono:

- L'utente da testare 

- Le app cloud, che l'utente si tenta di accedere

- Le condizioni in cui l'accesso per il cloud consente di configurare App viene eseguita
     
Come passaggio successivo, è possibile avviare una simulazione di esecuzione che restituisce le impostazioni. Solo i criteri sono abilitati fanno parte della valutazione di un'esecuzione.


Al termine della valutazione, lo strumento genera un report dei criteri interessati.


## <a name="running-the-tool"></a>Eseguire lo strumento

È possibile trovare il **cosa accade se** strumento il  **[accesso condizionale - criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)**  pagina nel portale di Azure.

Per avviare lo strumento, nella barra degli strumenti nella parte superiore dell'elenco dei criteri, fare clic su **cosa accade se**.

![E se](./media/active-directory-conditional-access-whatif/01.png)

Prima di poter eseguire una valutazione, è necessario configurare le impostazioni.

## <a name="settings"></a>Impostazioni

In questa sezione fornisce informazioni sulle impostazioni della simulazione eseguire.

![E se](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Utente

È possibile selezionare solo un utente. Questo è l'unico campo obbligatorio.

### <a name="cloud-apps"></a>App cloud

Il valore predefinito per questa impostazione è **tutte le app cloud**. L'impostazione predefinita esegue una versione di valutazione di tutti i criteri disponibili nell'ambiente in uso. È possibile restringere l'ambito di criteri che interessano le app cloud specifico.


### <a name="ip-address"></a>Indirizzo IP

L'indirizzo IP è un singolo indirizzo IPv4 per simulare il [condizione percorso](active-directory-conditional-access-azure-portal.md#locations). L'indirizzo rappresenta l'indirizzo del dispositivo usato dall'utente di accedere per Internet. È possibile verificare, ad esempio, l'indirizzo IP di un dispositivo, passare a [che cos'è l'indirizzo IP](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Piattaforme del dispositivo

Questa impostazione riproduce il [condizione piattaforme di dispositivo](active-directory-conditional-access-azure-portal.md#device-platforms) e rappresenta l'equivalente di **tutte le piattaforme (incluse non supportato)**. 
### <a name="client-apps"></a>App client

Questa impostazione riproduce il [condizione di App client](active-directory-conditional-access-azure-portal.md#client-apps).
Per impostazione predefinita, questa impostazione a causa di una versione di valutazione di tutti i criteri **Browser** o **App per dispositivi mobili e i client desktop** sia singolarmente sia selezionata. Rileva inoltre criteri che definiscono **Exchange ActiveSync (EAS)**. È possibile limitare questa impostazione, selezionare:

- **Browser** per valutare tutti i criteri con almeno **Browser** selezionato. 

- **App per dispositivi mobili e i client desktop** per valutare tutti i criteri con almeno **App per dispositivi mobili e i client desktop** selezionato. 


### <a name="sign-in-risk"></a>Rischio di accesso

Questa impostazione riproduce il [Accedi rischio condition](active-directory-conditional-access-azure-portal.md#sign-in-risk).   


## <a name="evaluation"></a>Versione di valutazione 

Avvio di una versione di valutazione, fare clic su **cosa accade se**. Il risultato della valutazione si fornisce un report che è costituito da: 

![E se](./media/active-directory-conditional-access-whatif/03.png)

- Un indicatore esistono criteri classici nell'ambiente in uso
- Criteri che si applicano all'utente
- Criteri che non si applicano all'utente


Se [criteri classici](active-directory-conditional-access-migration.md#classic-policies) esiste per le app cloud selezionato, viene visualizzato un indicatore. Facendo clic sull'indicatore, si viene reindirizzati alla pagina criteri classico. Nella pagina criteri classica, è possibile eseguire la migrazione di un classico criterio o disattivarlo. È possibile restituire il risultato della valutazione, chiudere questa pagina.

Nell'elenco dei criteri che si applicano all'utente selezionato, è anche possibile trovare un elenco di [concedere controlli](active-directory-conditional-access-controls.md#grant-controls) e [sessione](active-directory-conditional-access-controls.md#session-controls) l'utente deve soddisfare i controlli.

Nell'elenco dei criteri che non si applicano all'utente, è possibile e anche di trovare i motivi per cui non si applicano questi criteri. Per ogni criterio elencato, il motivo rappresenta la prima condizione che non è stata soddisfatta. Una possibile causa di un criterio che non viene applicato è un criterio disabilitato perché non vengono valutate altre.   



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Se si desidera eseguire la migrazione di criteri classici, vedere [eseguire la migrazione di criteri classici nel portale di Azure](active-directory-conditional-access-migration.md)  
