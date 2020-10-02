---
title: Lo strumento di What If dell'accesso condizionale-Azure Active Directory
description: Informazioni su come è possibile comprendere l'impatto dei criteri di accesso condizionale nell'ambiente in uso.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: bebd32d2773ca86638de221d305079bb262e3e5e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631427"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Risoluzione dei problemi di Accesso condizionale tramite lo strumento What If

[L'accesso condizionale](./overview.md) è una funzionalità di Azure Active Directory (Azure ad) che consente di controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Come è possibile sapere cosa aspettarsi dai criteri di accesso condizionale nell'ambiente in uso? Per rispondere a questa domanda, è possibile usare lo **strumento di What If dell'accesso condizionale**.

Questo articolo illustra come usare questo strumento per testare i criteri di accesso condizionale.

## <a name="what-it-is"></a>Che cos'è

Lo **strumento criteri di What If di accesso condizionale** consente di comprendere l'impatto dei criteri di accesso condizionale nell'ambiente in uso. Anziché testare i criteri eseguendo più accessi in modo manuale, questo strumento consente di valutare un accesso simulato di un utente. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione. Il report non elenca solo i criteri di accesso condizionale applicati, ma anche i [criteri classici](policy-migration.md#classic-policies) eventualmente esistenti.    

Lo strumento **What If** fornisce un modo per determinare rapidamente i criteri applicati a un utente specifico. Queste informazioni possono essere usate, ad esempio, se è necessario risolvere un problema.    

## <a name="how-it-works"></a>Funzionamento

Nello **strumento di What If dell'accesso condizionale**, è prima di tutto necessario configurare le impostazioni dello scenario di accesso che si vuole simulare. Le impostazioni includono:

- L'utente da testare 
- Le app cloud a cui l'utente proverà ad accedere
- Condizioni in base alle quali viene eseguito l'accesso alle app Cloud configurate
     
Come passaggio successivo, è possibile avviare una simulazione per valutare le impostazioni. Una valutazione prende in esame solo i criteri abilitati.

Al termine della valutazione, lo strumento genera un report dei criteri interessati. Per raccogliere altre informazioni sui criteri di accesso condizionale, la [cartella di lavoro di report e informazioni sull'accesso condizionale](howto-conditional-access-insights-reporting.md) può fornire dettagli aggiuntivi sui criteri in modalità solo report e tali criteri sono attualmente abilitati.

## <a name="running-the-tool"></a>Esecuzione dello strumento

È possibile trovare lo strumento **What If** nella pagina **[accesso condizionale-criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** della portale di Azure.

Per avviare lo strumento, sulla barra degli strumenti nella parte superiore dell'elenco di criteri fare clic su **What If**.

![What If](./media/what-if-tool/01.png)

Prima di eseguire una valutazione, è necessario configurare le impostazioni.

## <a name="settings"></a>Impostazioni

Questa sezione fornisce informazioni sulle impostazioni della simulazione.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Utente

È possibile selezionare un solo utente. Questo è l'unico campo obbligatorio.

### <a name="cloud-apps"></a>App cloud

Il valore predefinito di questa impostazione è **Tutte le app cloud**. Con l'impostazione predefinita viene eseguita una valutazione di tutti i criteri disponibili nell'ambiente in uso. È possibile restringere l'ambito di valutazione ai criteri che interessano app cloud specifiche.

### <a name="ip-address"></a>Indirizzo IP

L'indirizzo IP è un singolo indirizzo IPv4 necessario per simulare la [condizione di posizione](location-condition.md). Rappresenta l'indirizzo per Internet del dispositivo usato dall'utente per eseguire l'accesso. È possibile verificare l'indirizzo IP di un dispositivo accedendo, ad esempio, al sito Web [What is my IP address](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Piattaforme per dispositivi

Questa impostazione simula la [condizione di piattaforme del dispositivo](concept-conditional-access-conditions.md#device-platforms) ed equivale all'opzione **Tutte le piattaforme (incluse quelle non supportate)**. 

### <a name="client-apps"></a>App client

Questa impostazione simula la [condizione di app client](concept-conditional-access-conditions.md#client-apps).
Per impostazione predefinita, esegue la valutazione di tutti i criteri per i quali è selezionato **Browser** o **App per dispositivi mobili e client desktop** o sono selezionate entrambe le opzioni. Rileva anche i criteri che applicano **Exchange ActiveSync (EAS)**. Per restringere l'ambito di questa impostazione, selezionare:

- **Browser** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **Browser**. 
- **App per dispositivi mobili e client desktop** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **App per dispositivi mobili e client desktop**. 

### <a name="sign-in-risk"></a>Rischio di accesso

Questa impostazione simula la [condizione di rischio di accesso](concept-conditional-access-conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Valutazione 

Per avviare una valutazione, fare clic su **What If**. Al termine della valutazione, viene generato un report contenente gli elementi seguenti: 

![What If](./media/what-if-tool/03.png)

- Un indicatore che segnala l'eventuale presenza di criteri classici nell'ambiente in uso
- I criteri che si applicano all'utente
- I criteri che non si applicano all'utente

Se per le app cloud selezionate sono presenti [criteri classici](policy-migration.md#classic-policies), viene visualizzato un indicatore. Facendo clic sull'indicatore, l'utente viene reindirizzato alla pagina relativa ai criteri classici. In questa pagina è possibile semplicemente disabilitare un criterio classico o eseguirne la migrazione. Chiudendo la pagina è possibile tornare ai risultati della valutazione.

Nell'elenco dei criteri che si applicano all'utente selezionato è possibile trovare anche un elenco di controlli di [concessione](concept-conditional-access-grant.md) e [controlli di sessione](concept-conditional-access-session.md) che l'utente deve soddisfare.

Nell'elenco dei criteri che non si applicano all'utente è possibile trovare anche i motivi di tale inapplicabilità. Per ogni criterio elencato, il motivo rappresenta la prima condizione che non è stata soddisfatta. Un possibile motivo per cui un criterio non viene applicato è che sia stato disabilitato. In questo caso, il criterio non viene ulteriormente valutato.   

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'applicazione dei criteri di accesso condizionale sono reperibili usando la modalità di sola segnalazione dei criteri con informazioni dettagliate sull' [accesso condizionale e creazione di report](howto-conditional-access-insights-reporting.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere [criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md).
