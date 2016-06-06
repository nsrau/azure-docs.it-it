<properties
	pageTitle="Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sui servizi cloud di Azure | Microsoft Azure"
	description="Informazioni sulle operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sui servizi cloud di Azure."
	services="cloud-services"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="cloud-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sui servizi cloud di Azure

Microsoft si impegna costantemente per garantire agli utenti la disponibilità dei servizi in base alle esigenze. A volte si verificano eventi al di fuori del controllo di Microsoft, che causano interruzioni non pianificate dei servizi.

Microsoft fornisce un Contratto di servizio come impegno per garantire connettività e tempi di attività. Il Contratto di servizio per i singoli servizi di Azure è disponibile in [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

Azure offre già numerose funzionalità di piattaforma integrate che supportano applicazioni a disponibilità elevata. Per altre informazioni su questi servizi, vedere l'articolo relativo al [ripristino di emergenza e alla disponibilità elevata per applicazioni Azure](https://aka.ms/drtechguide).

Questo documento illustra un caso reale di ripristino di emergenza, quando in un'intera area si verifica un'interruzione a causa di una grave emergenza naturale o di un'interruzione diffusa del servizio. Si tratta di episodi rari, ma è necessario prepararsi alla possibilità che si verifichi un'interruzione nell'intera area. Se in un'intera area si verifica un'interruzione del servizio, le copie ridondanti locali dei dati non saranno disponibili temporaneamente. Se è stata abilitata la replica geografica, esistono altre tre copie dei BLOB e delle tabelle di Archiviazione di Azure archiviate in un'area diversa. Nel caso di un'interruzione completa dell'alimentazione a livello locale o di un'emergenza in cui l'area primaria non è recuperabile, Azure esegue un nuovo mapping di tutte le voci DNS all'area con replica geografica.
 
>[AZURE.NOTE]Tenere presente che non è possibile controllare questo processo e che verrà eseguito solo in caso di errori a livello di data center. Per questo motivo, è necessario affidarsi anche ad altre strategie di backup specifiche dell'applicazione per ottenere il massimo livello di disponibilità. Per altre informazioni, vedere la sezione relativa alle [strategie di dati per il ripristino di emergenza](https://aka.ms/drtechguide#DSDR). Per poter influire sul failover è consigliabile prendere in considerazione l'uso dell'[archiviazione con ridondanza geografica e accesso in lettura](../storage/storage-redundancy.md#read-access-geo-redundant-storage) che consente di creare una copia di sola lettura dei dati in un'altra area.

Per facilitare la gestione di tali rari episodi, vengono fornite le seguenti indicazioni per la macchina virtuale di Azure nel caso di un'interruzione del servizio dell'intera area in cui viene distribuita l'applicazione della macchina virtuale di Azure.

##Opzione 1: Attendere il ripristino 
In tal caso, non è necessaria alcuna azione da parte dell'utente, dato che i team di Azure sono impegnati per ripristinare la disponibilità del servizio. È possibile vedere lo stato corrente del servizio nel [dashboard sull'integrità dei servizi di Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Si tratta dell'opzione migliore se il cliente non ha configurato Azure Site Recovery (ASR) o ha una distribuzione secondaria in un'area diversa.

Per i clienti che desiderano accesso immediato ai servizi cloud distribuiti, sono disponibili le opzioni seguenti.

>[AZURE.NOTE]Tenere presente che se si usano le opzioni seguenti, può verificarsi una perdita dei dati.

##Opzione 2: Distribuire nuovamente la configurazione del servizio cloud in una nuova area 

Se si ha il codice originale, è possibile ridistribuire semplicemente l'applicazione nonché le risorse e la configurazione associate in un nuovo servizio cloud in una nuova area.

Per altre informazioni, vedere [Come creare e distribuire un servizio cloud](./cloud-services-how-to-create-deploy-portal.md).

In base alle origini dati dell'applicazione, può essere necessario controllare le procedure di ripristino per l'origine dati dell'applicazione.
  * Per le origini dati di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md#read-access-geo-redundant-storage) per controllare le opzioni disponibili in base al modello di replica scelto per l'applicazione.
  * Per le origini di Database SQL, leggere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md) per controllare le opzioni disponibili in base al modello di replica scelto per l'applicazione. 

##Opzione 3: Usare una distribuzione di backup tramite Gestione traffico 
Questa opzione presuppone che la soluzione dell'applicazione con il ripristino di emergenza locale sia già stata progettata. Questa opzione può essere usata se si dispone già di una distribuzione dell'applicazione del servizio cloud secondaria in esecuzione in un'area diversa e connessa tramite un canale di Gestione traffico. In questo caso, controllare l'integrità della distribuzione secondaria e, se integra, reindirizzare il traffico a tale distribuzione tramite Gestione traffico di Azure sfruttando il metodo di routing del traffico e le configurazioni dell'ordine failover in ATM, vedere [Come configurare le impostazioni di Gestione traffico](../traffic-manager/traffic-manager-overview.md#how-to-configure-traffic-manager-settings).

![Bilanciamento dei servizi cloud di Azure in diverse aree con Gestione traffico di Azure](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

##Riferimenti 

[Ripristino di emergenza e disponibilità elevata per le applicazioni di Azure](https://aka.ms/drtechguide)

[Informazioni tecniche sulla continuità aziendale di Azure](https://aka.ms/bctechguide)
 
Se le istruzioni non sono chiare, o se si desidera che Microsoft esegua le operazioni per proprio conto, contattare il [supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0525_2016-->