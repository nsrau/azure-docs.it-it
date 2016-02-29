<properties
   pageTitle="Utilizzo di SAP in Macchine virtuali di Azure | Microsoft Azure"
   description="Utilizzo di SAP in Macchine virtuali di Azure"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="juergent"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="sedusch"/>
   
# Utilizzo di SAP in Macchine virtuali di Azure

Cloud computing è un termine ampiamente diffuso che sta assumendo un'importanza sempre più rilevante nel settore IT, dalle piccole imprese fino alle grandi aziende e alle multinazionali. Microsoft Azure è la piattaforma di servizi cloud di Microsoft che offre un'ampia gamma di nuove possibilità. I clienti possono infatti effettuare rapidamente il provisioning e il deprovisioning di applicazioni come servizi cloud, senza essere soggetti a eventuali limiti tecnici o di budget. Anziché investire tempo e denaro nell'infrastruttura hardware, le aziende possono concentrarsi sull'applicazione, sui processi aziendali e sui vantaggi per clienti e utenti.

Grazie ai servizi inclusi in Macchine virtuali di Microsoft Azure, Microsoft offre una piattaforma di infrastruttura distribuita come servizio (IaaS) completa. Le applicazioni basate su SAP NetWeaver sono supportate in Macchine virtuali di Azure (IaaS). I white paper seguenti descrivono come pianificare e implementare applicazioni basate su SAP NetWeaver nella piattaforma Microsoft Azure.

## Pianificazione e implementazione

Titolo: SAP NetWeaver in Macchine virtuali di Azure - Guida alla pianificazione e all'implementazione

Riepilogo: Si tratta del primo documento da esaminare se si sta valutando la possibilità di eseguire SAP NetWeaver in Macchine virtuali di Azure. Questa Guida alla pianificazione e implementazione permette di valutare se è possibile distribuire un sistema basato su SAP NetWeaver esistente o pianificato in un ambiente di Macchine virtuali di Azure. Illustra vari scenari di distribuzione di SAP NetWeaver e include configurazioni SAP specifiche per Azure. Il documento elenca e descrive tutte le informazioni di configurazione necessarie relative a SAP/Azure per eseguire un landscape SAP ibrido. Vengono anche descritte le misure che è possibile adottare per garantire la disponibilità elevata dei sistemi basati su SAP NetWeaver nella soluzione IaaS.

Ultimo aggiornamento: agosto 2015

[Scaricare la guida](http://go.microsoft.com/fwlink/?LinkId=397963)
## Distribuzione
Titolo: SAP NetWeaver in Macchine virtuali di Azure - Guida alla distribuzione

Riepilogo: In questo documento vengono fornite indicazioni generali sulle procedure per la distribuzione del software SAP NetWeaver nelle macchine virtuali in Azure. Questo documento descrive tre scenari di distribuzione specifici, con particolare attenzione all'abilitazione delle estensioni di monitoraggio di Azure per SAP, inclusi alcuni suggerimenti per la risoluzione dei problemi delle estensioni di monitoraggio di Azure per SAP. Prima di passare a questo documento, è necessario aver letto la Guida alla pianificazione e all'implementazione.

Ultimo aggiornamento: settembre 2015

[Scaricare la guida](http://go.microsoft.com/fwlink/?LinkId=397964)

## DBMS per SAP in Azure
Titolo: Guida alla distribuzione di DBMS per SAP in Azure

Riepilogo: Questo documento presenta le considerazioni sulla pianificazione e l'implementazione per i sistemi DBMS che dovranno essere eseguiti con SAP. Nella prima parte vengono elencate e presentate alcune considerazioni di carattere generale. Le parti successive descrivono invece le distribuzioni supportate da SAP di diversi sistemi DBMS in Azure. Vari DBMS presentati sono SQL Server, SAP ASE, Oracle, SAP MaxDB e IBM DB2 per Linux, Unix e Windows. In queste sezioni più specifiche vengono illustrate le considerazioni di cui è necessario tenere conto quando vengono eseguiti sistemi SAP con tali sistemi DBMS in Azure. Vengono presentati argomenti quali i metodi di backup e disponibilità elevata supportati dai diversi sistemi DBMS in Azure per l'utilizzo con applicazioni SAP.

Ultimo aggiornamento: dicembre 2015

[Scaricare la guida](http://go.microsoft.com/fwlink/?LinkId=397965)

## SAP NetWeaver in Azure

Titolo: SAP NetWeaver - Creazione di una soluzione di ripristino di emergenza basata su Azure

Riepilogo: Questo documento fornisce istruzioni dettagliate per la creazione di una soluzione di ripristino di emergenza basata su Azure per SAP NetWeaver. La soluzione descritta presuppone che il landscape SAP sia in esecuzione virtualizzato in locale e basato su Hyper-V. Nella prima parte del documento vengono presentati i servizi e i componenti inclusi in Azure Site Recovery. La seconda parte del documento descrive le specifiche per il landscape basati su SAP NetWeaver. Viene anche descritta la possibilità di usare Azure Site Recovery con istanze dell'applicazione SAP NetWeaver e SAP Central Services. Sempre nella seconda parte viene illustrato in dettaglio come usare Azure Site Recovery per i servizi di SAP Central Services protetti tramite configurazioni cluster di failover di Windows Server.

Ultimo aggiornamento: settembre 2015

[Scaricare la guida](http://go.microsoft.com/fwlink/?LinkID=521971)

## SAP NetWeaver in Azure - Disponibilità elevata

Titolo: SAP NetWeaver in Azure - Clustering di istanze SAP ASCS/SCS tramite cluster di failover di Windows Server in Azure con SIOS DataKeeper

Riepilogo: Questo documento descrive come usare SIOS DataKeeper per definire una configurazione SAP ASCS/SCS a disponibilità elevata in Azure. SAP protegge i componenti con singolo punto di errore, ad esempio SAP ASCS/SCS o Enqueue Replication Services tramite configurazioni cluster di failover di Windows Server che richiedono dischi condivisi. Questi componenti SAP sono essenziali per la funzionalità di un sistema SAP. È quindi necessario implementare la disponibilità elevata per assicurarsi che tali componenti siano in grado di sostenere l'errore di un server o di una macchina virtuale come avviene con le configurazioni dei cluster Windows per gli ambienti Hyper-V e bare metal. Al momento, Azure non è in grado di fornire i dischi condivisi necessari per configurazioni a disponibilità elevata basate su Windows richieste per tali componenti SAP critici. Tuttavia, grazie a DataKeeper di SIOS, è possibile creare le configurazioni cluster di failover di Windows Server necessarie per SAP ASCS/SCS sulla piattaforma IaaS di Azure. Questo documento descrive la procedura dettagliata per installare una configurazione cluster di failover di Windows Server con disco condiviso fornito da SIOS Datakeeper in Azure, nonché le configurazioni dettagliate sul lato Azure, Windows e SAP necessarie per garantire il funzionamento ottimale della configurazione con disponibilità elevata. Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note su SAP che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

Ultimo aggiornamento: agosto 2015

[Scaricare la guida](http://go.microsoft.com/fwlink/?LinkId=613056)

## SAP NetWeaver nelle macchine virtuali SUSE Linux di Azure

Titolo: Test di SAP NetWeaver nelle macchine virtuali SUSE Linux di Microsoft Azure

Riepilogo: attualmente, non è previsto alcun supporto ufficiale di SAP per l'esecuzione di SAP NetWeaver nelle macchine virtuali Linux di Azure. Tuttavia, i clienti potrebbero richiedere di eseguire alcuni test o considerare di eseguire SAP dimostrativi o sistemi di formazione in macchine virtuali Linux di Azure fino a quando non è necessario contattare il supporto SAP. In questo articolo viene descritto come configurare le macchine virtuali SUSE Linux di Azure per l'esecuzione di SAP; inoltre, vengono forniti alcuni suggerimenti di base per evitare errori potenziali comuni.

Ultimo aggiornamento: dicembre 2015

[Questo articolo è disponibile qui](virtual-machines-sap-on-linux-suse-quickstart.md)

<!---HONumber=AcomDC_0218_2016-->