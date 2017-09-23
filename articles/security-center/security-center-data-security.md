---
title: Sicurezza dei dati nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come i dati vengono gestiti e protetti nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 6f95cf7631664f4630edbbcdadfd1d98105fdb98
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017

---
# <a name="azure-security-center-data-security"></a>Sicurezza dei dati nel Centro sicurezza di Azure
Per consentire ai clienti di impedire, rilevare e rispondere alle minacce, il Centro sicurezza di Azure raccoglie ed elabora dati correlati alla sicurezza, tra cui informazioni di configurazione, metadati, registri eventi, file di dump di arresto anomalo del sistema e altro. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.

Questo articolo illustra come i dati vengono gestiti e protetti nel Centro sicurezza di Azure.

>[!NOTE] 
>A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>


## <a name="data-sources"></a>Origini dati
Il Centro sicurezza di Azure analizza i dati provenienti dalle origini seguenti per offrire visibilità sullo stato della sicurezza, identificare le vulnerabilità e suggerire le mitigazioni e rilevare minacce attive:

- Servizi di Azure: usa le informazioni sulla configurazione dei servizi di Azure distribuiti comunicando con il provider di risorse del servizio.
- Traffico di rete: usa i metadati del traffico di rete campionati dall'infrastruttura di Microsoft, ad esempio l'IP/porta di origine/destinazione, le dimensioni del pacchetto e il protocollo di rete.
- Soluzioni partner: usa gli avvisi di sicurezza dalle soluzioni partner integrate, ad esempio firewall e soluzioni antimalware. 
- Macchine virtuali e server: usa informazioni sulla configurazione e sugli eventi di sicurezza, ad esempio log di controllo e log eventi di Windows, log di IIS, messaggi syslog e file di dump di arresto anomalo del sistema dalle macchine virtuali. Quando viene creato un avviso, il Centro sicurezza di Azure può anche generare uno snapshot del disco della VM interessato ed estrarre gli elementi del computer relativi all'avviso dal disco della VM, ad esempio un file del Registro di sistema, a scopo di analisi.


## <a name="data-protection"></a>Protezione dati
**Separazione dei dati:**i dati vengono mantenuti separati logicamente in ogni componente del servizio. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio.

**Accesso ai dati**: per offrire raccomandazioni sulla sicurezza e analizzare le potenziali minacce alla sicurezza, il personale Microsoft può accedere alle informazioni raccolte o analizzate dai servizi di Azure, inclusi i file di dump di arresto anomalo del sistema, eventi di creazione di un processo, snapshot ed elementi del disco della VM, che potrebbero accidentalmente includere dati della società o dati personali provenienti dalle macchine virtuali. Microsoft rispetta le [condizioni di Microsoft Online Services e l'Informativa sulla privacy](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), in cui è specificato che Microsoft non userà i dati del cliente o ne ricaverà informazioni per scopi pubblicitari o commerciali simili. Microsoft userà i dati dei clienti solo se necessari per fornire i servizi di Azure, incluse le finalità compatibili con la fornitura di tali servizi. L'utente conserva tutti i diritti sui dati dei clienti.

**Uso dei dati**: Microsoft usa modelli e intelligence per le minacce trovati in più tenant per migliorare le funzionalità di prevenzione e rilevamento, in base alle garanzie relative alla privacy descritte nell'[Informativa sulla privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Posizione dei dati

**Aree di lavoro**: per le seguenti aree geografiche è specificata un'area di lavoro e i dati raccolti dalle macchine virtuali di Azure, inclusi i dump di arresto anomalo del sistema e alcuni tipi di dati di avviso, vengono archiviati nell'area di lavoro più vicina. 

| Area geografica VM                        | Area geografica area di lavoro |
|-------------------------------|---------------|
| Stati Uniti, Brasile, Canada | Stati Uniti |
| Europa, Regno Unito        | Europa        |
| Asia Pacifico, Giappone, India    | Asia/Pacifico  |
| Australia                     | Australia     |

 
Gli snapshot del disco della VM vengono archiviati nello stesso account di archiviazione del disco della VM.
 
Per le macchine virtuali e i server in esecuzione in altri ambienti, ad esempio in locale, è possibile specificare l'area di lavoro e l'area in cui vengono archiviati i dati raccolti. 

**Archiviazione in Centro sicurezza di Azure**: le informazioni sugli avvisi di sicurezza, inclusi gli avvisi dei partner, vengono archiviate a livello regionale in base alla posizione della risorsa di Azure correlata, mentre le informazioni sullo stato di integrità della sicurezza e le raccomandazioni vengono archiviate a livello centrale negli Stati Uniti o in Europa in base alla posizione del cliente.
Il Centro sicurezza di Azure raccoglie copie temporanee dei file di dump di arresto anomalo del sistema e le analizza per cercare le prove di tentativi di exploit e compromissioni riuscite. Il Centro sicurezza di Azure esegue questa analisi nella stessa area geografica dell'area di lavoro ed elimina le copie temporanee al termine dell'analisi.

Gli elementi del computer vengono archiviati centralmente nella stessa area della VM. 


## <a name="managing-data-collection-from-virtual-machines"></a>Gestione della raccolta dati da macchine virtuali

Quando si abilita il Centro sicurezza in Azure, viene attivata la raccolta dati per ogni sottoscrizione di Azure. È anche possibile attivare la raccolta dati per le sottoscrizioni esistenti nella sezione Criteri di sicurezza del Centro sicurezza di Azure. Quando la raccolta dati è attivata, il Centro sicurezza di Azure effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate esistenti e in quelle nuove che vengono create. Microsoft Monitoring Agent esegue l'analisi delle varie configurazioni correlate alla sicurezza e ne genera gli eventi nelle tracce di [Event Tracing for Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx). Il sistema operativo genererà anche eventi del log eventi durante l'esecuzione del computer. Esempi di tali dati sono: tipo e versione del sistema operativo, log del sistema operativo (registri eventi di Windows), processi in esecuzione, nome computer, indirizzi IP, utente connesso e ID tenant. Microsoft Monitoring Agent legge le voci del log eventi ed ETW le traccia e le copia nelle aree di lavoro per l'analisi. Microsoft Monitoring Agent copia anche i file di dump di arresto anomalo del sistema nelle aree di lavoro.

Se si usa la versione gratuita del Centro sicurezza di Azure, è anche possibile disabilitare la raccolta dati dalle macchine virtuali nei criteri di sicurezza. La raccolta dati è richiesta per le sottoscrizioni a livello Standard. Gli snapshot dei dischi delle VM e la raccolta di elementi resteranno abilitati anche se la raccolta dati è stata disabilitata.


## <a name="see-also"></a>Vedere anche
Questo documento ha illustrato come i dati vengono gestiti e protetti nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza di Azure, vedere:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md) : informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure

