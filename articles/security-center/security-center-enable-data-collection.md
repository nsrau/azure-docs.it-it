---
title: Raccolta dati nel Centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come abilitare la raccolta dati nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2018
ms.author: rkarlin
ms.openlocfilehash: a5151d1f9498b29c79638445a58a8337abff8961
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281923"
---
# <a name="data-collection-in-azure-security-center"></a>Raccolta dati nel Centro sicurezza di Azure
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer che non hanno Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite Microsoft Monitoring Agent, che legge diverse configurazioni correlate alla sicurezza oltre ai registri eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Esempi di tali dati sono: tipo e versione del sistema operativo, log del sistema operativo (registri eventi di Windows), processi in esecuzione, nome computer, indirizzi IP, utente connesso, eventi AppLocker e ID tenant. Microsoft Monitoring Agent copia anche i file di dump di arresto anomalo del sistema nelle aree di lavoro.

> [!NOTE]
> Per abilitare la raccolta dei dati per i [controlli applicazione adattivi](security-center-adaptive-application.md), Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. In questo modo AppLocker genererà eventi che vengono poi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 
>

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Abilitare il provisioning automatico di Microsoft Monitoring Agent     
Il provisioning automatico è disattivato per impostazione predefinita. Dopo aver abilitato il provisioning automatico, il Centro sicurezza effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create. Il provisioning automatico è fortemente consigliato, ma è disponibile anche l'installazione manuale dell'agente. [Informazioni sull'installazione dell'estensione Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> - La disabilitazione automatica del provisioning limita il monitoraggio delle risorse. Per altre informazioni, vedere [Disabilitare il provisioning automatico](security-center-enable-data-collection.md#disable-automatic-provisioning) in questo articolo. Gli snapshot del disco della macchina virtuale e la raccolta di elementi resteranno abilitati anche se il provisioning automatico viene disabilitato.
>

Per abilitare il provisioning automatico di Microsoft Monitoring Agent:
1. Selezionare **Criteri di sicurezza** nel menu principale Centro sicurezza.
2. Selezionare la sottoscrizione.

  ![Selezionare la sottoscrizione][7]

3. In **Criteri di sicurezza** selezionare **Raccolta dati**.
4. In **Provisioning automatico** selezionare **Attivato** per abilitare il provisioning automatico.
5. Selezionare **Salva**.

  ![Abilitare il provisioning automatico][1]

## <a name="default-workspace-configuration"></a>Configurazione dell'area di lavoro predefinita
I dati raccolti dal Centro sicurezza vengono archiviati nell'area di lavoro di Log Analytics.  È possibile scegliere di archiviare i dati raccolti dalle macchine virtuali di Azure nelle aree di lavoro create dal Centro sicurezza o in un'area di lavoro esistente creata dall'utente.

Per usare l'area di lavoro di Log Analytics esistente:
- L'area di lavoro deve essere associata alla sottoscrizione di Azure selezionata.
- È necessario avere almeno le autorizzazioni di lettura per accedere all'area di lavoro.

Per selezionare l'area di lavoro di Log Analytics esistente:

1. In **Configurazione dell'area di lavoro predefinita** selezionare **Usa un'altra area di lavoro**.

   ![Selezionare un'area di lavoro esistente][2]

2. Nel menu a discesa selezionare un'area di lavoro dove archiviare i dati raccolti.

  > [!NOTE]
  > Nel menu a discesa sono disponibile tutte le aree di lavoro delle sottoscrizioni. Per altre informazioni, vedere [Selezione di un'area di lavoro tra sottoscrizioni](security-center-enable-data-collection.md#cross-subscription-workspace-selection).
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

## <a name="cross-subscription-workspace-selection"></a>Selezione di un'area di lavoro tra sottoscrizioni
Quando si seleziona un'area di lavoro per archiviare i dati, sono disponibili tutte le aree di lavoro delle sottoscrizioni. La selezione di un'area di lavoro tra sottoscrizioni consente di raccogliere i dati da macchine virtuali in esecuzione in diverse sottoscrizioni e di archiviarli nell'area di lavoro scelta. Questa funzionalità può essere usata sia per le macchine virtuali in esecuzione su Windows che su Linux.

> [!NOTE]
> La selezione di un'area di lavoro tra sottoscrizioni è un componente del livello gratuito del Centro sicurezza di Azure. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

## <a name="data-collection-tier"></a>Livello della raccolta dati
Il Centro sicurezza può ridurre il volume di eventi mantenendo un numero sufficiente di eventi per l'analisi, il controllo e il rilevamento delle minacce. È possibile scegliere i criteri di filtraggio corretti per le sottoscrizioni e le aree di lavoro da quattro insiemi di eventi raccolti dall'agente.

- **Tutti gli eventi**: per i clienti che desiderano assicurarsi che tutti gli eventi vengano raccolti. Questa è la modalità predefinita.
- **Comuni**: si tratta di un insieme di eventi che soddisfa la maggior parte dei clienti e consente loro di avere un audit trail completo.
- **Minimi**: un insieme più piccolo di eventi per i clienti che desiderano ridurre al minimo il volume di eventi.
- **Nessuno**: per disabilitare la raccolta di eventi di sicurezza dai registri di sicurezza e di App Locker. Per i clienti che scelgono questa opzione, i dashboard di sicurezza presentano solo i registri di Windows Firewall e le valutazioni proattive come antimalware, iniziali e di aggiornamento.

> [!NOTE]
> Questi set di eventi di sicurezza sono disponibili solo nel livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
Questi insiemi sono stati progettati per soddisfare gli scenari tipici. Assicurarsi di valutare quale di questi si adatti alle proprie esigenze prima di implementarlo.
>
>

Per determinare gli eventi che apparterranno all'insieme di eventi **Comuni** e **Minimi** abbiamo collaborato con i clienti e usato gli standard del settore per conoscere la frequenza non filtrata di ogni evento e il loro uso. In questo processo sono state usate le linee guida seguenti:

- **Minimi**: assicurarsi che questo insieme includa solo gli eventi che potrebbero indicare una violazione riuscita ed eventi importanti con un volume molto basso. Ad esempio, questo insieme può contenere accessi utente riusciti e non (ID evento 4624, 4625), però non contiene disconnessioni importanti per il controllo ma non per il rilevamento e ha un volume relativamente alto. La maggior parte del volume di dati di questo insieme è composto da eventi di accesso e da eventi di creazione del processo (ID evento 4688).
- **Comuni**: fornire un audit trail completo degli utenti in questo insieme. Ad esempio, questo insieme contiene sia gli accessi utente che le disconnessioni dell'utente (ID evento 4634). Sono incluse azioni di controllo quali modifiche al gruppo di sicurezza, operazioni Kerberos del controller del dominio principale e altri eventi consigliati dalle organizzazioni del settore.

Gli eventi che hanno un volume molto basso sono stati inclusi nell'insieme Comuni. Questo insieme è stato scelto perché l'obiettivo è quello di ridurre il volume, non di filtrare eventi specifici.

Di seguito è riportata una suddivisione completa degli ID di eventi di sicurezza e App Locker per ogni insieme:

| Livello dati | Indicatori di eventi raccolti |
| --- | --- |
| Minime | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Comune | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se si usa l'oggetto Criteri di gruppo, è consigliabile abilitare i criteri di controllo della creazione del processo dell'evento 4688 e il campo *CommandLine* all'interno dell'evento 4688. Per altre informazioni sulla creazione del processo dell'evento 4688, vedere le [domande frequenti](security-center-faq.md#what-happens-when-data-collection-is-enabled) del Centro sicurezza. Per altre informazioni su questi criteri di controllo, vedere [Suggerimenti per i criteri di controllo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Per abilitare la raccolta dei dati per i [controlli applicazione adattivi](security-center-adaptive-application.md), Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. In questo modo AppLocker genererà eventi che vengono poi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 
>

Per scegliere i criteri di filtraggio:
1. Nel pannello **Criteri di sicurezza - Raccolta di dati** selezionare i criteri di filtraggio in **Eventi di sicurezza**.
2. Selezionare **Salva**.

   ![Scegliere i criteri di filtraggio][5]

## <a name="disable-automatic-provisioning"></a>Disabilitare il provisioning automatico
È possibile disabilitare il provisioning automatico nelle risorse in qualsiasi momento disattivando questa impostazione nei criteri di sicurezza. Il provisioning automatico è fortemente consigliato per ottenere gli avvisi di sicurezza e i suggerimenti sugli aggiornamenti del sistema, le vulnerabilità del sistema operativo e la protezione degli endpoint.

> [!NOTE]
> La disabilitazione del provisioning automatico non rimuove Microsoft Monitoring Agent dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente.
>
>

1. Tornare al menu principale del Centro sicurezza e selezionare Criteri di sicurezza.
2. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico.
3. Nel pannello **Criteri di sicurezza - Raccolta di dati**, in **Provisioning automatico** selezionare **Disattivato**.
4. Selezionare **Salva**.

  ![Disabilitare il provisioning automatico][6]

Quando il provisioning automatico è disabilitato (disattivato), la sezione di configurazione dell'area di lavoro predefinita non viene visualizzata.

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
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
