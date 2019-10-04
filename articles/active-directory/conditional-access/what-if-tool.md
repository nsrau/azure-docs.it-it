---
title: Che cos'è la cosa se strumento in Active Directory accesso condizionale di Azure?
description: Informazioni su come è possibile comprendere l'impatto dei criteri di accesso condizionale nell'ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/20/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97d2ec4099045e17b8482fcde313d31720083583
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506747"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access"></a>Che cos'è la cosa se strumento in Active Directory accesso condizionale di Azure?

[Accesso condizionale](../active-directory-conditional-access-azure-portal.md) è una funzionalità di Azure Active Directory (Azure AD) che consente al controllo del modo in cui gli utenti autorizzati accedono alle App cloud. Come si sapere cosa accade tra i criteri di accesso condizionale nell'ambiente in uso? Per rispondere a questa domanda, è possibile usare la **condizionale simulazione accesso strumento**.

Questo articolo illustra come è possibile usare questo strumento per testare i criteri di accesso condizionale.

## <a name="what-it-is"></a>Che cos'è

Il **strumento per i criteri condizionali simulazione accesso** consente di comprendere l'impatto dei criteri di accesso condizionale nell'ambiente. Anziché testare i criteri eseguendo più accessi in modo manuale, questo strumento consente di valutare un accesso simulato di un utente. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione. Il report non sono elencati solo i criteri di accesso condizionale applicati, ma anche [i criteri classici](policy-migration.md#classic-policies) se presenti.    

Il **cosa accade se** strumento consente di individuare rapidamente i criteri applicati a un utente specifico. Queste informazioni possono essere usate, ad esempio, se è necessario risolvere un problema.    

## <a name="how-it-works"></a>Funzionamento

Nel **condizionale simulazione accesso strumento**, è innanzitutto necessario configurare le impostazioni dello scenario si desidera simulare l'accesso. Queste impostazioni includono:

- L'utente da testare 
- Le app cloud a cui l'utente proverà ad accedere
- Le condizioni in cui viene eseguito l'accesso alle app cloud configurate
     
Come passaggio successivo, è possibile avviare una simulazione per valutare le impostazioni. Una valutazione prende in esame solo i criteri abilitati.

Al termine della valutazione, lo strumento genera un report dei criteri interessati.

## <a name="running-the-tool"></a>Esecuzione dello strumento

È possibile trovare il **cosa accade se** dello strumento sul **[accesso condizionale - criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** pagina nel portale di Azure.

Per avviare lo strumento, sulla barra degli strumenti sopra l'elenco dei criteri, fare clic su **cosa accade se**.

![What If](./media/what-if-tool/01.png)

Prima di eseguire una valutazione, è necessario configurare le impostazioni.

## <a name="settings"></a>Impostazioni

Questa sezione fornisce informazioni sulle impostazioni della simulazione.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Utente

È possibile selezionare un solo utente. Questo campo è l'unico obbligatorio.

### <a name="cloud-apps"></a>App cloud

Il valore predefinito di questa impostazione è **Tutte le app cloud**. Con l'impostazione predefinita viene eseguita una valutazione di tutti i criteri disponibili nell'ambiente in uso. È possibile restringere l'ambito di valutazione ai criteri che interessano app cloud specifiche.

### <a name="ip-address"></a>Indirizzo IP

L'indirizzo IP è un singolo indirizzo IPv4 necessario per simulare la [condizione di posizione](location-condition.md). Rappresenta l'indirizzo per Internet del dispositivo usato dall'utente per eseguire l'accesso. È possibile verificare l'indirizzo IP di un dispositivo accedendo, ad esempio, al sito Web [What is my IP address](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Piattaforme del dispositivo

Questa impostazione simula la [condizione di piattaforme del dispositivo](conditions.md#device-platforms) ed equivale all'opzione **Tutte le piattaforme (incluse quelle non supportate)** . 

### <a name="client-apps"></a>App client

Questa impostazione simula la [condizione di app client](conditions.md#client-apps).
Per impostazione predefinita, esegue la valutazione di tutti i criteri per i quali è selezionato **Browser** o **App per dispositivi mobili e client desktop** o sono selezionate entrambe le opzioni. Rileva anche i criteri che applicano **Exchange ActiveSync (EAS)** . Per restringere l'ambito di questa impostazione, selezionare:

- **Browser** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **Browser**. 
- **App per dispositivi mobili e client desktop** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **App per dispositivi mobili e client desktop**. 

### <a name="sign-in-risk"></a>Rischio di accesso

Questa impostazione simula la [condizione di rischio di accesso](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Versione di valutazione 

Avviare una valutazione facendo **cosa accade se**. Al termine della valutazione, viene generato un report contenente gli elementi seguenti: 

![What If](./media/what-if-tool/03.png)

- Un indicatore che segnala l'eventuale presenza di criteri classici nell'ambiente in uso
- I criteri che si applicano all'utente
- I criteri che non si applicano all'utente

Se per le app cloud selezionate sono presenti [criteri classici](policy-migration.md#classic-policies), viene visualizzato un indicatore. Facendo clic sull'indicatore, l'utente viene reindirizzato alla pagina relativa ai criteri classici. In questa pagina è possibile semplicemente disabilitare un criterio classico o eseguirne la migrazione. Chiudendo la pagina è possibile tornare ai risultati della valutazione.

Nell'elenco dei criteri che si applicano all'utente selezionato è possibile trovare anche un elenco di [controlli di concessione](controls.md#grant-controls) e controlli di [sessione](controls.md#session-controls) che l'utente deve soddisfare.

Nell'elenco dei criteri che non si applicano all'utente è possibile trovare anche i motivi di tale inapplicabilità. Per ogni criterio elencato, il motivo rappresenta la prima condizione che non è stata soddisfatta. Un possibile motivo per cui un criterio non viene applicato è che sia stato disabilitato. In questo caso, il criterio non viene ulteriormente valutato.   

## <a name="next-steps"></a>Passaggi successivi

- Se si desidera sapere come configurare un criterio di accesso condizionale, vedere [Richiedi autenticazione a più fattori per App specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente, vedere la [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 
- Per informazioni sulla migrazione dei criteri classici, vedere [Eseguire la migrazione dei criteri classici nel portale di Azure](policy-migration.md).  
