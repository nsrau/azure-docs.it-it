<properties
   pageTitle="Sicurezza dei dati nel Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento illustra come i dati vengono gestiti e protetti nel Centro sicurezza di Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="yurid"/>

# Sicurezza dei dati nel Centro sicurezza di Azure
Per consentire ai clienti di impedire, rilevare e rispondere alle minacce, il Centro sicurezza di Azure raccoglie ed elabora dati sulle risorse di Azure, tra cui informazioni di configurazione, metadati, registri eventi, file di dump di arresto anomalo del sistema e altro. Microsoft è fortemente impegnata nella protezione della privacy e della sicurezza dei dati. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.

Questo articolo illustra come i dati vengono gestiti e protetti nel Centro sicurezza di Azure.

## Origini dati
Il Centro sicurezza di Azure analizza i dati dalle origini seguenti:

- Servizi di Azure: legge le informazioni sulla configurazione dei servizi di Azure distribuiti comunicando con il provider di risorse del servizio.
- Traffico di rete: legge i metadati del traffico di rete campionati dall'infrastruttura di Microsoft, ad esempio l'IP/porta di origine/destinazione, le dimensioni del pacchetto e il protocollo di rete.
- Soluzioni partner: raccoglie gli avvisi di sicurezza dalle soluzioni partner integrate, ad esempio firewall e soluzioni antimalware. Questi dati vengono salvati nella risorsa di archiviazione Centro sicurezza di Azure, che attualmente si trova negli Stati Uniti.
- Macchine virtuali: il Centro sicurezza di Azure può raccogliere informazioni sulla configurazione e informazioni sugli eventi di sicurezza, ad esempio registri eventi di Windows e log di controllo, log di IIS, messaggi syslog e file di dump di arresto anomalo del sistema dalle macchine virtuali usando gli agenti di raccolta dati. Vedere più avanti la sezione "Gestione della raccolta dati" per altri dettagli.

Anche le informazioni sugli avvisi di sicurezza, le raccomandazioni e lo stato integrità della sicurezza vengono archiviate nella risorsa di archiviazione Centro sicurezza di Azure, che attualmente si trova negli Stati Uniti. Queste informazioni possono includere informazioni sulla configurazione correlate ed eventi di sicurezza raccolti dalle macchine virtuali, necessari per conoscere l'avviso di sicurezza, la raccomandazione o lo stato integrità della sicurezza.

## Protezione dati
**Separazione dei dati:** i dati vengono mantenuti separati logicamente in ogni componente del servizio. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio. I dati raccolti dalle macchine virtuali vengono anche archiviati negli account di archiviazione.

**Accesso ai dati**: per fornire raccomandazioni sulla sicurezza ed analizzare le potenziali minacce alla sicurezza,il personale Microsoft può accedere alle informazioni raccolte o analizzate dai servizi di Azure, inclusi i file di dump di arresto anomalo del sistema. I file di dump di arresto anomalo del sistema e gli eventi di creazione di un processo possono involontariamente includere dati dei clienti o dati personali dalle macchine virtuali. Microsoft rispetta le [Condizioni per l'Utilizzo dei Servizi Online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e l'[Informativa sulla privacy Servizi online Microsoft](https://www.microsoft.com/privacystatement/it-IT/OnlineServices/Default.aspx), in cui è specificato che Microsoft non userà i dati del cliente o ne ricaverà informazioni per scopi pubblicitari o commerciali simili. Microsoft userà i dati dei clienti solo se necessari per fornire i servizi di Azure, incluse le finalità compatibili con la fornitura di tali servizi. L'utente conserva tutti i diritti sui dati dei clienti.

**Utilizzo dei dati**: Microsoft usa modelli e intelligence per le minacce trovati in più tenant per migliorare le funzionalità di prevenzione e rilevamento, in base alle garanzie relative alla privacy descritte nell'[Informativa sulla privacy Servizi online Microsoft](https://www.microsoft.com/privacystatement/it-IT/OnlineServices/Default.aspx).

**Posizione dei dati**: viene specificato un account di archiviazione per ogni area in cui sono in esecuzione macchine virtuali. Questo consente di archiviare i dati nella stessa area della macchina virtuale da cui vengono raccolti i dati. I dati, inclusi i file di dump di arresto anomalo del sistema, verranno memorizzati in modo permanente nell'account di archiviazione. Il servizio archivia anche le informazioni sugli avvisi di sicurezza, inclusi gli avvisi provenienti da soluzioni partner integrate, le raccomandazioni e lo stato integrità della sicurezza nella risorsa di archiviazione Centro sicurezza di Azure, che attualmente si trova negli Stati Uniti.

## Gestione della raccolta dati da macchine virtuali

Quando si sceglie di abilitare il Centro sicurezza di Azure, viene attivata la raccolta dati per ogni sottoscrizione. È possibile disattivare la raccolta dati nella sezione "Criteri di sicurezza" del dashboard del Centro sicurezza di Azure. Quando la raccolta dati è attivata, il Centro sicurezza di Azure effettua il provisioning dell'agente di monitoraggio di Azure in tutte le macchine virtuali supportate esistenti e in quelle nuove che vengono create. L'estensione per il monitoraggio della sicurezza di Azure esegue l'analisi delle varie configurazioni correlate alla sicurezza e ne genera gli eventi nelle tracce di [Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Il sistema operativo genererà anche eventi del log eventi durante l'esecuzione del computer. Esempi di tali dati sono: tipo e versione del sistema operativo, log del sistema operativo (registri eventi di Windows), processi in esecuzione, nome computer, indirizzi IP, utente connesso e ID tenant. L'agente di monitoraggio di Azure legge le voci del registro eventi ed ETW le traccia e le copia nell'account di archiviazione per l'analisi.

Viene specificato un account di archiviazione per ogni area in cui si hanno macchine virtuali in esecuzione, in cui i dati raccolti dalle macchine virtuali vengono archiviati in quella stessa area. Ciò semplifica la conservazione dei dati nella stessa area geografica per scopi di sovranità dei dati e di privacy. È possibile configurare account di archiviazione per ogni area nella sezione "Criteri di sicurezza" del dashboard del Centro sicurezza di Azure.

L'agente di monitoraggio di Azure copia anche i file di dump di arresto anomalo del sistema nell'account di archiviazione. Il Centro sicurezza di Azure raccoglie copie temporanee dei file di dump di arresto anomalo del sistema e le analizza per cercare le prove di tentativi di exploit e compromissioni riuscite. Il Centro sicurezza di Azure esegue questa analisi nella stessa area geografica dell'account di archiviazione ed elimina le copie temporanee al termine dell'analisi.

È possibile disabilitare la raccolta dati dalle macchine virtuali in qualsiasi momento. Gli agenti di monitoraggio già installati dal Centro sicurezza di Azure verranno rimossi.


## Passaggi successivi

Questo documento ha illustrato come i dati vengono gestiti e protetti nel Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza di Azure, vedere:

- [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni sulla pianificazione e considerazioni di progettazione per l'adozione del Centro sicurezza di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!---HONumber=AcomDC_0817_2016-->