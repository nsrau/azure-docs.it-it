---
title: Raccolta dati nel Centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come abilitare la raccolta dati nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="data-collection-in-azure-security-center"></a>Raccolta dati nel Centro sicurezza di Azure
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer che non hanno Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite Microsoft Monitoring Agent, che legge diverse configurazioni correlate alla sicurezza oltre ai registri eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Esempi di tali dati sono: tipo e versione del sistema operativo, log del sistema operativo (registri eventi di Windows), processi in esecuzione, nome computer, indirizzi IP, utente connesso e ID tenant. Microsoft Monitoring Agent copia anche i file di dump di arresto anomalo del sistema nelle aree di lavoro.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Abilitare il provisioning automatico di Microsoft Monitoring Agent     
Dopo aver abilitato il provisioning automatico, il Centro sicurezza effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create. Il provisioning automatico è fortemente consigliato ed è obbligatorio per le sottoscrizioni del livello Standard del Centro sicurezza.

> [!NOTE]
> La disabilitazione automatica del provisioning limita il monitoraggio delle risorse. Per altre informazioni, vedere [Disabilitare il provisioning automatico](security-center-enable-data-collection.md#disable-automatic-provisioning) in questo articolo. Gli snapshot del disco della macchina virtuale e la raccolta di elementi resteranno abilitati anche se il provisioning automatico viene disabilitato.
>
>

Per abilitare il provisioning automatico di Microsoft Monitoring Agent:
1. selezionare **Criteri di sicurezza** nel menu principale Centro sicurezza.
2. Selezionare la sottoscrizione.
3. In **Criteri di sicurezza** selezionare **Raccolta dati**.
4. In **Onboarding** selezionare **On** (Abilita) per abilitare il provisioning automatico.
5. Selezionare **Salva**.

![Abilitare il provisioning automatico][1]

## <a name="default-workspace-configuration"></a>Configurazione dell'area di lavoro predefinita
I dati raccolti dal Centro sicurezza vengono archiviati nell'area di lavoro di Log Analytics.  È possibile scegliere di archiviare i dati raccolti dalle macchine virtuali di Azure nelle aree di lavoro create dal Centro sicurezza o in un'area di lavoro esistente creata dall'utente.

Per usare l'area di lavoro di Log Analytics esistente:
- l'area di lavoro deve essere associata alla sottoscrizione di Azure selezionata.
- È necessario avere almeno le autorizzazioni di lettura per accedere all'area di lavoro.

Per selezionare l'area di lavoro di Log Analytics esistente:

1. In **Security policy – Data Collection** (Criteri di sicurezza - Raccolta dati) selezionare **Use another workspace** (Usare un'altra area di lavoro).

   ![Selezionare un'area di lavoro esistente][2]

2. Nel menu a discesa selezionare un'area di lavoro dove archiviare i dati raccolti.

> [!NOTE]
> Nel menu a discesa vengono visualizzate solo le aree di lavoro a cui si ha accesso e che si trovano nella sottoscrizione di Azure.
>
>

3. Selezionare **Salva**.
4. Dopo aver selezionato **Salva**, verrà richiesto se si desidera riconfigurare le macchine virtuali monitorate.

   - Selezionare **No** se si desidera che le nuove impostazioni dell'area di lavoro si applichino solo alle nuove macchine virtuali. Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni dell'agente, ovvero alle macchine virtuali da poco rilevate che non hanno installato Microsoft Monitoring Agent.
   - Selezionare **Sì** se si desidera che le nuove impostazioni dell'area di lavoro si applichino a tutte le macchine virtuali. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

   > [!NOTE]
   > Se si seleziona Sì, non è necessario eliminare le aree di lavoro create dal Centro sicurezza fino a quando tutte le macchine virtuali vengono ricollegate alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.
   >
   >

   - Selezionare **Annulla** per annullare l'operazione.

   ![Selezionare un'area di lavoro esistente][3]

## <a name="data-collection-tier"></a>Livello della raccolta dati
Il Centro sicurezza può ridurre il volume di eventi mantenendo un numero sufficiente di eventi per l'analisi, il controllo e il rilevamento delle minacce. È possibile scegliere i criteri di filtraggio corretti per le sottoscrizioni e le aree di lavoro da quattro insiemi di eventi raccolti dall'agente.

- **Tutti gli eventi**: per i clienti che desiderano assicurarsi che tutti gli eventi vengano raccolti. Questa è la modalità predefinita.
- **Comuni**: si tratta di un insieme di eventi che soddisfa la maggior parte dei clienti e consente loro di avere un audit trail completo.
- **Minimi**: un insieme più piccolo di eventi per i clienti che desiderano ridurre al minimo il volume di eventi.
- **Nessuno**: per disabilitare la raccolta di eventi di sicurezza dai registri di sicurezza e di App Locker. Per i clienti che scelgono questa opzione, i dashboard di sicurezza presentano solo i registri di Windows Firewall e le valutazioni proattive come antimalware, iniziali e di aggiornamento.

> [!NOTE]
> Questi insiemi sono stati progettati per soddisfare gli scenari tipici. Assicurarsi di valutare quale di questi si adatti alle proprie esigenze prima di implementarlo.
>
>

Per determinare gli eventi che apparterranno all'insieme di eventi **Comuni** e **Minimi** abbiamo collaborato con i clienti e usato gli standard del settore per conoscere la frequenza non filtrata di ogni evento e il loro uso. In questo processo sono state usate le linee guida seguenti:

- **Minimi**: assicurarsi che questo insieme includa solo gli eventi che potrebbero indicare una violazione riuscita ed eventi importanti con un volume molto basso. Ad esempio, questo insieme può contenere accessi utente riusciti e non (ID evento 4624, 4625), però non contiene disconnessioni importanti per il controllo ma non per il rilevamento e ha un volume relativamente alto. La maggior parte del volume di dati di questo insieme è composto da eventi di accesso e da eventi di creazione del processo (ID evento 4688).
- **Comuni**: fornire un audit trail completo degli utenti in questo insieme. Ad esempio, questo insieme contiene sia gli accessi utente che le disconnessioni dell'utente (ID evento 4634). Sono incluse azioni di controllo quali modifiche al gruppo di sicurezza, operazioni Kerberos del controller del dominio principale e altri eventi consigliati dalle organizzazioni del settore.

Gli eventi che hanno un volume molto basso sono stati inclusi nell'insieme Comuni. Questo insieme è stato scelto perché l'obiettivo è quello di ridurre il volume, non di filtrare eventi specifici.

Di seguito è riportata una suddivisione completa degli ID di eventi di sicurezza e App Locker per ogni insieme:

   ![ID evento][4]

Per scegliere i criteri di filtraggio:
1. Nel pannello **Security policy & settings** (Criteri e impostazioni di sicurezza) selezionare i criteri di filtraggio in **Eventi di sicurezza**.
2. Selezionare **Salva**.

   ![Scegliere i criteri di filtraggio][5]

## <a name="disable-automatic-provisioning"></a>Disabilitare il provisioning automatico
È possibile disabilitare il provisioning automatico nelle risorse in qualsiasi momento disattivando questa impostazione nei criteri di sicurezza. Il provisioning automatico è fortemente consigliato per ottenere gli avvisi di sicurezza e i suggerimenti sugli aggiornamenti del sistema, le vulnerabilità del sistema operativo e la protezione degli endpoint.

> [!NOTE]
> La disabilitazione del provisioning automatico non rimuove Microsoft Monitoring Agent dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente.
>
>

1. Tornare al menu principale del Centro sicurezza e selezionare Criteri di sicurezza.

   ![Disabilitare il provisioning automatico][6]

2. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico.
3. Nel pannello **Security policy – Data Collection** (Criteri di sicurezza - Raccolta dati), in **Onboarding** selezionare **Disattiva** per disabilitare il provisioning automatico.
4. Selezionare **Salva**.  

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato il funzionamento della raccolta dati e del provisioning automatico nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni sulla gestione e la protezione dei dati nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png

