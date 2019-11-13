---
title: Aggiornare un cluster di Azure Service Fabric
description: Informazioni sull'aggiornamento della versione o della configurazione di un cluster di Service Fabric.  Questo articolo descrive l'impostazione della modalità di aggiornamento di un cluster, l'aggiornamento dei certificati, l'aggiunta di porte dell'applicazione e l'applicazione di patch del sistema operativo. Descrive anche cosa aspettarsi quando vengono eseguiti gli aggiornamenti
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: 161c720fbcc9370aaf273b241e88a7184f47371b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013311"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Aggiornamento di un cluster di Azure Service Fabric

Per i sistemi attuali la progettazione a livello di aggiornamento è fondamentale per il successo a lungo termine di un prodotto. Un cluster di Azure Service Fabric è una risorsa di proprietà dell'utente parzialmente gestita da Microsoft. Questo articolo descrive ciò che viene gestito automaticamente e ciò che è possibile configurare manualmente.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controllo della versione di Fabric eseguita nel cluster

Verificare che il cluster esegua sempre una [versione di Fabric supportata](service-fabric-versions.md). Quando viene annunciato il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel blog del team di Service Fabric. A questo punto è possibile scegliere la nuova versione.

14 giorni prima della scadenza della versione in esecuzione nel cluster, viene generato un evento di integrità e il cluster passa a uno stato di avviso. Il cluster rimane in stato di avviso fino a quando non viene eseguito l'aggiornamento a una versione di Fabric supportata.

È possibile impostare il cluster per ricevere gli aggiornamenti automatici di Fabric quando vengono rilasciati da Microsoft oppure è possibile selezionare una versione di Fabric supportata da eseguire nel cluster.  Per altre informazioni, vedere [Aggiornare la versione di Service Fabric del cluster](service-fabric-cluster-upgrade-version-azure.md).

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>Comportamento dell'aggiornamento di Fabric durante gli aggiornamenti automatici
Microsoft gestisce il codice dell'infrastruttura e la configurazione eseguita in un cluster di Azure e, in base alle esigenze, esegue aggiornamenti monitorati del software in modo automatico. Gli aggiornamenti possono interessare il codice, la configurazione o entrambi. Per assicurarsi che gli aggiornamenti abbiano un impatto minimo o nullo sull'applicazione, vengono eseguiti nelle fasi descritte di seguito.

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Un aggiornamento viene eseguito usando tutti i criteri di integrità del cluster
In questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

* Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
* Eventuali azioni correttive suggerite.
* Il numero di giorni (n) prima dell'avvio della fase 2.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 2.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita. in modo da evitare l'invio di troppi messaggi e per far sì che la ricezione di un messaggio rappresenti un'eccezione alla norma. Si prevede che la maggior parte degli aggiornamenti del cluster abbia esito positivo, senza alcun impatto sulla disponibilità dell'applicazione.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Un aggiornamento viene eseguito usando solo i criteri di integrità predefiniti
I criteri di integrità in questa fase vengono impostati in modo che il numero di applicazioni integre all'inizio dell'aggiornamento rimanga invariato per l'intera durata del processo di aggiornamento. Come nella fase 1, in questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. I criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione sul cluster) vengono rispettati per l'intera durata dell'aggiornamento.

Se i criteri di integrità del cluster in vigore non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

* Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
* Eventuali azioni correttive suggerite.
* Il numero di giorni (n) prima dell'avvio della fase 3.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Viene inviato un messaggio di sollecito alcuni giorni prima della scadenza degli n giorni. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 3. I messaggi di posta elettronica inviati nella fase 2 devono essere tenuti in alta considerazione e devono essere intraprese azioni correttive.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Un aggiornamento viene eseguito usando criteri di integrità aggressivi
Tali criteri di integrità in questa fase sono pensati per il completamento dell'aggiornamento piuttosto che per l'integrità delle applicazioni. In questa fase vengono eseguiti pochi aggiornamenti del cluster. Se il cluster giunge a questa fase, è molto probabile che l'applicazione divenga non integra e/o perda disponibilità.

In modo analogo alle altre due fasi, gli aggiornamenti della fase 3 procedono in base a un dominio di aggiornamento alla volta.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. A questo punto, il cluster viene bloccato in modo che non riceva più supporto e/o aggiornamenti.

Al proprietario della sottoscrizione viene inviato un messaggio di posta elettronica contenente queste informazioni e le azioni correttive. Se la fase 3 ha esito negativo, si prevede che il cluster non venga impostato su alcun tipo di stato.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

## <a name="manage-certificates"></a>Gestire i certificati
Service Fabric usa i [certificati server X.509](service-fabric-cluster-security.md) specificati quando si crea un cluster per proteggere le comunicazioni tra i nodi del cluster ed eseguire l'autenticazione client. È possibile aggiungere, aggiornare o eliminare certificati per il cluster e il client nel [portale di Azure](https://portal.azure.com) o tramite Azure PowerShell o l'interfaccia della riga di comando di Azure.  Per altre informazioni, vedere [Aggiungere o rimuovere certificati](service-fabric-cluster-security-update-certs-azure.md)

## <a name="open-application-ports"></a>Aprire porte dell'applicazione
È possibile modificare le porte dell'applicazione modificando le proprietà della risorsa del servizio di bilanciamento del carico associate al tipo di nodo. È possibile usare il portale di Azure oppure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Aprire le porte dell'applicazione per un cluster](create-load-balancer-rule.md).

## <a name="define-node-properties"></a>Definire le proprietà dei nodi
È talvolta necessario assicurarsi che carichi di lavoro specifici vengano eseguiti solo in tipi di nodo specifici nel cluster. Ad esempio, è possibile che alcuni carichi di lavoro richiedano GPU o SSD, mentre altri no. Per ogni tipo di nodo in un cluster è possibile aggiungere proprietà personalizzate ai nodi corrispondenti. I vincoli di posizionamento sono le istruzioni collegate ai singoli servizi che selezionano una o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti.

Per informazioni dettagliate sull'uso e sulla definizione dei vincoli di posizionamento e delle proprietà dei nodi, vedere [Proprietà dei nodi e vincoli di posizionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="add-capacity-metrics"></a>Aggiungere metriche di capacità
Per ogni tipo di nodo è possibile aggiungere metriche di capacità personalizzate da usare nelle applicazioni per creare report sul carico. Per informazioni dettagliate sull'uso di metriche di capacità per la segnalazione del carico, vedere i documenti di Gestione risorse del cluster di Service Fabric relativi a [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [metriche e carico](service-fabric-cluster-resource-manager-metrics.md).

## <a name="set-health-policies-for-automatic-upgrades"></a>Impostare criteri di integrità per gli aggiornamenti automatici
È possibile specificare criteri di integrità personalizzati per l'aggiornamento di Fabric. Se nel cluster è stato impostato l'aggiornamento automatico di Fabric, questi criteri vengono applicati alla fase 1 degli aggiornamenti automatici di Fabric.
Se nel cluster è stato impostato l'aggiornamento manuale di Fabric, questi criteri vengono applicati ogni volta che si seleziona una nuova versione attivando nel sistema l'avvio dell'aggiornamento di Fabric nel cluster. Se non si esegue l'override dei criteri, vengono usati quelli predefiniti.

È possibile specificare criteri di integrità personalizzati o esaminare le impostazioni correnti nel pannello relativo agli aggiornamenti di Fabric, selezionando le impostazioni di aggiornamento avanzate. La figura seguente illustra questo passaggio. 

![Gestire criteri di integrità personalizzati][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>Personalizzare le impostazioni di Fabric per il cluster
In un cluster è possibile personalizzare molte impostazioni di configurazione diverse, ad esempio il livello di affidabilità del cluster e le proprietà dei nodi. Per altre informazioni, vedere [Impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Patch del sistema operativo nei nodi del cluster
Patch Orchestration Application (POA) è un'applicazione Service Fabric che automatizza l'applicazione di patch nei sistemi operativi in un cluster di Service Fabric senza tempi di inattività. È possibile eseguire la distribuzione di [Patch Orchestration Application per Windows](service-fabric-patch-orchestration-application.md) nel cluster per installare patch in modo orchestrato, mantenendo i servizi sempre disponibili.


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md)
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
