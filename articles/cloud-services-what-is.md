<properties 
	pageTitle="Informazioni sul servizio cloud - Gestione servizi di Azure" 
	description="Introduzione al servizio cloud in Azure." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>




#Informazioni sul servizio cloud
Quando si crea un'applicazione e la si esegue in Azure, la combinazione del codice e della configurazione costituisce il cosiddetto servizio cloud (noto come *hosted service* nelle versioni precedenti di Azure).

Creando un servizio cloud è possibile distribuire un'applicazione Web multilivello in Azure, definendo più ruoli per la distribuzione dell'elaborazione e la scalabilità flessibile dell'applicazione. Un servizio cloud è costituito da uno o più ruoli Web e/o ruoli di lavoro, ognuno con i rispettivi file e configurazione dell'applicazione. Siti Web e Macchine virtuali di Azure abilitano anche le applicazioni Web in Azure.  Il principale vantaggio dei servizi cloud consiste nella possibilità di supportare architetture multilivello più complesse. Per un confronto dettagliato, vedere [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure][Comparison].

Per un servizio cloud, Azure gestisce automaticamente l'infrastruttura, eseguendo la manutenzione di routine, applicando patch ai sistemi operativi e tentando di correggere gli errori hardware e del servizio. Se si definiscono almeno due istanze di ogni ruolo, è possibile eseguire la maggior parte della manutenzione, oltre che gli aggiornamenti del servizio, senza interrompere il servizio. Un servizio cloud deve avere almeno due istanze di ciascun ruolo per l'idoneità con il contratto di servizio di Azure che garantisce la connettività esterna ai ruoli con connessione Internet pari ad almeno il 99,95% del tempo. 

Ogni servizio cloud dispone di due ambienti in cui è possibile distribuire il pacchetto e la configurazione del servizio. È possibile distribuire un servizio cloud nell'ambiente di gestione temporanea per testarlo prima di promuoverlo alla produzione. Per promuovere un servizio cloud di gestione temporanea alla produzione, è sufficiente scambiare gli indirizzi IP virtuali (VIP) associati ai due ambienti. 


## Concetti ##


- **ruolo del servizio cloud:** un ruolo del servizio cloud è costituito da file dell'applicazione e da una configurazione. Un servizio cloud può avere due tipi di ruoli:
 
>- **ruolo Web:** un ruolo Web un server Web IIS (Internet Information Services) dedicato usato per ospitare applicazioni Web front-end.

>- **ruolo di lavoro:** le applicazioni ospitate nei ruoli di lavoro possono eseguire attività asincrone, di lunga durata o perpetue indipendenti dall'interazione o dall'input dell'utente.

- **istanza del ruolo:** un'istanza del ruolo è una macchina virtuale in cui vengono eseguiti il codice dell'applicazione e la configurazione del ruolo. Un ruolo può avere più istanze, definite nel file di configurazione del servizio.

- **sistema operativo guest:** il sistema operativo guest per un servizio cloud è il sistema operativo installato nelle istanze del ruolo (macchine virtuali) in cui viene eseguito il codice all'applicazione.

- **componenti del servizio cloud:** per distribuire un'applicazione come servizio cloud in Azure, sono necessari tre componenti:

>- **file di definizione del servizio:** il file di definizione del servizio (con estensione csdef) definisce il modello del servizio, incluso il numero di ruoli.

>- **file di configurazione del servizio:** il file di configurazione del servizio cloud (con estensione cscfg) specifica le impostazioni di configurazione per il servizio cloud e i singoli ruoli, incluso il numero di istanze del ruolo.

>- **pacchetto del servizio:** il pacchetto del servizio (con estensione cspkg) contiene il codice dell'applicazione e il file di definizione del servizio.

- **distribuzione del servizio cloud:** una distribuzione del servizio cloud è un'istanza di un servizio cloud distribuito nell'ambiente di gestione temporanea o di produzione di Azure. È possibile gestire le distribuzioni sia durante la gestione temporanea che durante la produzione.

- **ambienti di distribuzione:** Azure offre due ambienti di distribuzione per i servizi cloud: un *staging environment* in cui è possibile testare la distribuzione prima di promuoverla all' *production environment*. I due ambienti si distinguono solo per gli indirizzi IP virtuali (VIP, Virtual IP) da cui si accede al servizio cloud. Nell'ambiente di gestione temporanea l'identificatore univoco globale (GUID) del servizio cloud lo identifica negli URL (*GUID*.cloudapp.net). Nell'ambiente di produzione l'URL si basa sul prefisso DNS descrittivo assegnato al servizio cloud (ad esempio, *myservice*.cloudapp.net).

- **scambio di distribuzioni:** per promuovere una distribuzione nell'ambiente di gestione temporanea di Azure all'ambiente di produzione, è possibile "scambiare" le distribuzioni invertendo gli indirizzi VIP da cui si accede alle due distribuzioni. Dopo la distribuzione, il nome DNS per il servizio cloud punta alla distribuzione che si trovava nell'ambiente di gestione temporanea. 

- **monitoraggio minimo e dettagliato: il** *Minimal monitoring*, che viene configurato per impostazione predefinita per un servizio cloud, ricorre a contatori delle prestazioni raccolti dai sistemi operativi host per istanze del ruolo (macchine virtuali). Il *Verbose monitoring* raccoglie metriche supplementari in base ai dati delle prestazioni all'interno delle istanze del ruolo per consentire un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. Per altre informazioni, vedere [Come monitorare i servizi cloud][HTMonitorCloudServices].

- **Diagnostica Azure:** Diagnostica Azure è l'API che consente di raccogliere i dati di diagnostica dalle applicazioni in esecuzione in Azure. È necessario abilitare Diagnostica Azure per i ruoli del servizio cloud in modo da attivare il monitoraggio dettagliato. Per altre informazioni, vedere [Abilitazione della diagnostica in Azure][CloudServicesDiagnostics].

- **collegamento di una risorsa:** per visualizzare le dipendenze del servizio cloud da altre risorse, ad esempio un'istanza di database SQL di Azure, è possibile "collegare" la risorsa al servizio cloud. Nel portale di gestione in anteprima, è possibile visualizzare le risorse collegate nella pagina **Risorse collegate**, visualizzarne lo stato nel dashboard e scalare un'istanza di database SQL collegata insieme ai ruoli del servizio nella pagina **Scala**. Collegando una risorsa in questo modo non si connette la risorsa all'applicazione. È necessario configurare le connessioni al codice dell'applicazione.

- **scalabilità di un servizio cloud:** per scalare orizzontalmente un servizio cloud, è necessario aumentare il numero di istanze del ruolo (macchine virtuali) distribuite per un ruolo. Un servizio cloud viene scalato riducendo le istanze del ruolo. Nel portale di gestione di anteprima è inoltre possibile scalare un'istanza del database SQL collegata, modificando l'edizione del database SQL e la dimensione massima del database, quando si scalano i ruoli del servizio.

- **Contratto di servizio di Azure:** il Contratto di servizio di Calcolo di Azure garantisce che, quando si distribuiscono due o più istanze del ruolo per ogni ruolo, l'accesso al servizio cloud verrà mantenuto per almeno il 99,95% del tempo. Inoltre, il 99,9% delle volte verrà avviata un'azione di rilevamento e correttiva quando un processo dell'istanza del ruolo non è in esecuzione. Per altre informazioni, vedere [Contratto di servizio][SLA].

[HTMonitorCloudServices]:http://azure.microsoft.com/ manage/services/cloud-services/how-to-monitor-a-cloud-service/
[SLA]: http://azure.microsoft.com/ support/legal/sla/
[CloudServicesDiagnostics]: http://azure.microsoft.com/ documentation/articles/cloud-services-dotnet-diagnostics/
[Comparison]: http://azure.microsoft.com/ documentation/articles/choose-web-site-cloud-service-vm/

<!--HONumber=45--> 
