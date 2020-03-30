---
title: Correggere i consigli nel Centro sicurezza di Azure Documenti Microsoft
description: Questo articolo illustra come correggere i suggerimenti nel Centro sicurezza di Azure per proteggere le risorse e rispettare i criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603492"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Correzione delle raccomandazioni nel Centro sicurezza di Azure

I consigli ti forniscono suggerimenti su come proteggere al meglio le tue risorse. Implementare un suggerimento seguendo i passaggi di correzione forniti nella raccomandazione.

## <a name="remediation-steps"></a>Passaggi di correzioneRemediation steps<a name="remediation-steps"></a>

Dopo aver esaminato tutte le raccomandazioni, decidere quale correggere prima. Si consiglia di utilizzare [l'impatto del punteggio sicuro](security-center-recommendations.md#monitor-recommendations) per definire le priorità degli elementi da eseguire per primi.

1. Nell'elenco fare clic sul suggerimento.

1. Seguire le istruzioni nella sezione Passaggi di **correzione.** Ogni raccomandazione ha il proprio set di istruzioni. La schermata seguente mostra i passaggi di correzione per la configurazione delle applicazioni in modo da consentire solo il traffico su HTTPS.

    ![Dettagli delle raccomandazioni](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Al termine, viene visualizzata una notifica che informa l'utente se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation"></a>Correzione rapida della correzione<a name="one-click"></a>

Correzione rapida consente di correggere rapidamente un suggerimento su più risorse. È disponibile solo per raccomandazioni specifiche. Quick Fix semplifica la correzione e consente di aumentare rapidamente il punteggio sicuro, migliorando la sicurezza dell'ambiente.

Per implementare la correzione rapida:

1. Dall'elenco dei consigli che hanno la **correzione rapida!** , fare clic sulla raccomandazione.

    [![Selezionare Correzione rapida!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Nella scheda **Risorse non integre** selezionare le risorse per le quale si desidera implementare la raccomandazione e fare clic su **Correggi**.

    > [!NOTE]
    > Alcune delle risorse elencate potrebbero essere disabilitate, perché non si dispone delle autorizzazioni appropriate per modificarle.

1. Nella casella di conferma leggere i dettagli e le implicazioni della correzione.

    ![Correzione rapida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Le implicazioni sono elencate nella casella grigia nella finestra **Correggi risorse** che si apre dopo aver fatto clic su **Correggi**. Elencano le modifiche che si verificano quando si procede con la correzione rapida.

1. Inserire i parametri pertinenti, se necessario, e approvare la correzione.

    > [!NOTE]
    > Il completamento della correzione può richiedere alcuni minuti dopo il completamento della correzione per visualizzare le risorse nella scheda **Risorse integre.** Per visualizzare le azioni di correzione, controllare il [registro attività](#activity-log).

1. Al termine, viene visualizzata una notifica che informa l'utente se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Registrazione rapida correzione nel registro attività<a name="activity-log"></a>

L'operazione di monitoraggio e aggiornamento usa una distribuzione di modelli o una chiamata all'API REST PATCH per applicare la configurazione alla risorsa. Queste operazioni vengono registrate nel log attività di [Azure.](../azure-resource-manager/management/view-activity-logs.md)


## <a name="recommendations-with-quick-fix-remediation"></a>Suggerimenti per la correzione rapidaRecommendations with Quick Fix remediation

|Recommendation|Implicazione|
|---|---|
|Il controllo sui server SQL deve essere abilitato|Questa azione consentirà il controllo SQL su questi server e i relativi database. <br>**Nota**: <ul><li>Per ogni area dei server SQL selezionati, un account di archiviazione per il salvataggio dei log di controllo verrà creato e condiviso da tutti i server in tale area.</li><li>Per garantire il controllo corretto, non eliminare o rinominare il gruppo di risorse o gli account di archiviazione.</li></ul>|
|La sicurezza dei dati avanzata deve essere abilitata nelle istanze gestite di SQL|Questa azione abiliterà SQL Advanced Data Security (ADS) nelle istanze gestite SQL selezionate. <br>**Nota**: <ul><li>Per ogni area e gruppo di risorse delle istanze gestite SQL selezionate, un account di archiviazione per il salvataggio dei risultati dell'analisi verrà creato e condiviso da tutte le istanze in tale area.</li><li> ADS viene addebitato un importo di 15 USD per ogni istanza gestita SQL.</li></ul>|
|La valutazione delle vulnerabilità deve essere abilitata nelle istanze gestite di SQL|Questa azione consentirà la valutazione delle vulnerabilità SQL nelle istanze gestite SQL selezionate. <br>**Nota**:<ul><li>La valutazione delle vulnerabilità SQL fa parte del pacchetto SQL Advanced Data Security (ADS). Se ADS non è già abilitato, verrà abilitato automaticamente nell'istanza gestita.</li><li>Per ogni area e gruppo di risorse delle istanze gestite SQL selezionate, un account di archiviazione per l'archiviazione dei risultati dell'analisi verrà creato e condiviso da tutte le istanze in tale area.</li><li>ADS viene addebitato a 15 USD per ogni server SQL.</li></ul>||
|La sicurezza avanzata dei dati deve essere abilitata nei server SQLAdvanced Data Security should be enabled on your SQL servers|Questa azione abiliterà Advanced Data Security (ADS) su questi server selezionati e sui relativi database. <br>**Nota**:<ul><li>Per ogni area e gruppo di risorse dei server SQL selezionati, un account di archiviazione per l'archiviazione dei risultati dell'analisi verrà creato e condiviso da tutti i server in tale area.<</li><li>ADS viene addebitato a 15 USD per ogni server SQL.</li></ul>||
|La valutazione delle vulnerabilità deve essere abilitata nei server SQL|Questa azione consentirà la valutazione della vulnerabilità SQL su questi server selezionati e sui relativi database. <br>**Nota**:<ul><li>La valutazione delle vulnerabilità SQL fa parte del pacchetto SQL Advanced Data Security (ADS). Se ADS non è già abilitato, verrà abilitato automaticamente nel server SQL.</li><li>Per ogni area e gruppo di risorse dei server SQL selezionati, un account di archiviazione per l'archiviazione dei risultati dell'analisi verrà creato e condiviso da tutte le istanze in tale area.</li><li>ADS viene addebitato a 15 USD per ogni server SQL.</li></ul>||
|La crittografia trasparente dei dati nei database SQL deve essere abilitataTransparent data encryption on SQL databases should be enabled|Questa azione abilita SQL Database Transparent Data Encryption (TDE) nei database selezionati. <br>**Nota:** per impostazione predefinita, verranno utilizzate chiavi TDE gestite dal servizio.
|Il trasferimento sicuro negli account di archiviazione deve essere abilitato|Questa azione aggiorna la sicurezza dell'account di archiviazione per consentire solo le richieste tramite connessioni protette. (HTTPS). <br>**Nota**:<ul><li>Tutte le richieste che utilizzano HTTP verranno rifiutate.</li><li>Quando si usa il servizio file di Azure, la connessione senza crittografia avrà esito negativo, inclusi gli scenari che usano SMB 2.1, SMB 3.0 senza crittografia e alcune versioni del client SMB Linux.When you're using the Azure files service, connection without encryption will fail, including scenarios using SMB 2.1, SMB 3.0 without encryption, and some flavors of the Linux SMB client. Altre informazioni.</li></ul>|
|L'applicazione Web deve essere accessibile solo tramite HTTPS|Questa azione reindirizzerà tutto il traffico da HTTP a HTTPS, sulle risorse selezionate. <br>**Nota**:<ul><li>Un endpoint HTTPS che non dispone di un certificato SSL verrà visualizzato nel browser con un 'Errore di privacy'. Pertanto, gli utenti che dispongono di un dominio personalizzato devono verificare di aver configurato un certificato SSL.</li><li>Assicurarsi che i firewall dei pacchetti e delle applicazioni Web che proteggono il servizio app consentano l'inoltro delle sessioni HTTPS.</li></ul>|
|L'app per le funzioni deve essere accessibile solo tramite HTTPS|Questa azione reindirizzerà tutto il traffico da HTTP a HTTPS, sulle risorse selezionate. <br>**Nota**:<ul><li>Un endpoint HTTPS che non dispone di un certificato SSL verrà visualizzato nel browser con un 'Errore di privacy'. Pertanto, gli utenti che dispongono di un dominio personalizzato devono verificare di aver configurato un certificato SSL.</li><li>Assicurarsi che i firewall dei pacchetti e delle applicazioni Web che proteggono il servizio app consentano l'inoltro delle sessioni HTTPS.</li></ul>|
|L'app per le API deve essere accessibile solo tramite HTTPS|Questa azione reindirizzerà tutto il traffico da HTTP a HTTPS, sulle risorse selezionate. <br>**Nota**:<ul><li>Un endpoint HTTPS che non dispone di un certificato SSL verrà visualizzato nel browser con un 'Errore di privacy'. Pertanto, gli utenti che dispongono di un dominio personalizzato devono verificare di aver configurato un certificato SSL.</li><li>Assicurarsi che i firewall dei pacchetti e delle applicazioni Web che proteggono il servizio app consentano l'inoltro delle sessioni HTTPS.</li></ul>|
|Il debug remoto deve essere disattivato per l'applicazione Web|Questa azione disabilita il debug remoto.|
|Il debug remoto deve essere disattivato per l'app per le funzioni|Questa azione disabilita il debug remoto.|
|Il debug remoto deve essere disattivato per l'app per le API|Questa azione disabilita il debug remoto.|
|CORS non deve consentire a ogni risorsa l'accesso all'applicazione Web|Questa azione impedisce ad altri domini di accedere all'applicazione Web. Per consentire domini specifici, immetterli nel campo Origini consentite (separati da virgole). <br>**Nota:** lasciando vuoto il campo bloccherà tutte le chiamate tra origini incrociate.'Titolo campo di nome: 'Origini consentite'|
|Condivisione risorse tra le origini non deve consentire a tutte le risorse di accedere all'app per le funzioni dell'utente|Questa azione impedisce ad altri domini di accedere all'applicazione di funzione. Per consentire domini specifici, immetterli nel campo Origini consentite (separati da virgole). <br>**Nota:** lasciando vuoto il campo bloccherà tutte le chiamate tra origini incrociate.'Titolo campo di nome: 'Origini consentite'|
|CORS non deve consentire a tutte le risorse di accedere all'app per le API dell'utente|Questa azione impedisce ad altri domini di accedere all'applicazione API. Per consentire domini specifici, immetterli nel campo Origini consentite (separati da virgole). <br>**Nota:** lasciando vuoto il campo bloccherà tutte le chiamate tra origini incrociate.'Titolo campo di nome: 'Origini consentite'|
|L'agente di monitoraggio deve essere abilitato nelle macchine virtualiMonitoring agent should be enabled on your virtual machines|Questa azione consente di installare un agente di monitoraggio nelle macchine virtuali selezionate. Selezionare un'area di lavoro per l'agente in cui eseguire il report.<ul><li>Se il criterio di aggiornamento è impostato su automatico, verrà distribuito nelle nuove istanze esistenti.</li><li>Se i criteri di aggiornamento sono impostati su manuale e si desidera installare l'agente nelle istanze esistenti, selezionare l'opzione della casella di controllo. [Scopri di più](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|I log di diagnostica in Key Vault devono essere abilitati|Questa azione abilita i log di diagnostica sugli insiemi di credenziali delle chiavi. I log di diagnostica e le metriche vengono salvati nell'area di lavoro selezionata.|
|I log di diagnostica nel bus di servizio devono essere abilitati|Questa azione abilita i log di diagnostica nel bus di servizio. I log di diagnostica e le metriche vengono salvati nell'area di lavoro selezionata.|

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come correggere i suggerimenti nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:To learn more about Security Center, see the following topics:

* [Impostazione dei criteri](tutorial-security-policy.md) di sicurezza nel Centro sicurezza di Azure: informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.Setting security policies in Azure Security Center - Informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.Setting security policies in Azure Security Center -
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
