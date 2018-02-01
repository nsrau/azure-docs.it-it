---
title: Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come proteggere le distribuzioni di applicazioni SAP NetWeaver con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 96dc9bc81b8889e2e962c9c2dbf119ee985ec2f1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Proteggere una distribuzione di applicazioni SAP NetWeaver multilivello usando Azure Site Recovery

La maggior parte delle distribuzioni SAP di medie e grandi dimensioni prevede una soluzione di ripristino di emergenza.  L'importanza di soluzioni di ripristino di emergenza efficaci e testabili è aumentata con lo spostamento di un numero sempre maggiore di processi aziendali importanti in applicazioni come SAP.  Azure Site Recovery è stato testato e integrato con le applicazioni SAP e supera le capacità della maggior parte delle soluzioni di ripristino di emergenza locali, a un costo totale di proprietà inferiore rispetto alle soluzioni della concorrenza.
Con Azure Site Recovery è possibile:
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in locale, eseguendo la replica dei componenti in Azure.
* Abilitare la protezione di applicazioni di produzione SAP NetWeaver e non NetWeaver in esecuzione in Azure, eseguendo la replica dei componenti in un altro data center di Azure.
* Semplificare la migrazione nel cloud usando Site Recovery per eseguire la migrazione della distribuzione SAP in Azure.
* Semplificare gli aggiornamenti, i test e la creazione di prototipi dei progetti SAP, creando un clone di produzione on demand per i test delle applicazioni SAP.

Questo articolo descrive come proteggere le distribuzioni di applicazioni SAP NetWeaver con [Azure Site Recovery](site-recovery-overview.md). L'articolo illustra le procedure consigliate per la protezione di una distribuzione SAP NetWeaver a tre livelli in Azure tramite la replica in un altro data center di Azure usando Azure Site Recovery, le configurazioni e gli scenari supportati e come eseguire i failover, sia di test (esercitazioni sul ripristino di emergenza) che effettivi.


## <a name="prerequisites"></a>prerequisiti
Prima di iniziare, è necessario comprendere i concetti illustrati di seguito:

1. [Replica di una macchina virtuale in Azure](azure-to-azure-walkthrough-enable-replication.md)
2. Come [progettare una rete di ripristino](site-recovery-azure-to-azure-networking-guidance.md)
3. [Esecuzione di un failover di test in Azure](azure-to-azure-walkthrough-test-failover.md)
4. [Esecuzione di un failover in Azure](site-recovery-failover.md)
5. Come [replicare un controller di dominio](site-recovery-active-directory.md)
6. Come [replicare SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scenari supportati
Con Azure Site Recovery è possibile implementare una soluzione di ripristino di emergenza per gli scenari seguenti:
* Sistemi SAP in esecuzione in un data center di Azure con replica in un altro data center di Azure (ripristino di emergenza da Azure ad Azure), come illustrato [qui](https://aka.ms/asr-a2a-architecture).
* Sistemi SAP in esecuzione su server VMWare (o fisici) in locale con replica in un sito di ripristino di emergenza in un data center di Azure (ripristino di emergenza da VMware ad Azure), con la necessità di alcuni componenti aggiuntivi, come illustrato [qui](https://aka.ms/asr-v2a-architecture).
* Sistemi SAP in esecuzione in Hyper-V in locale con replica in un sito di ripristino di emergenza in un data center di Azure (ripristino di emergenza da Hyper-V ad Azure), con la necessità di alcuni componenti aggiuntivi, come illustrato [qui](https://aka.ms/asr-h2a-architecture).

Questo documento usa il primo caso, ovvero il ripristino di emergenza da Azure ad Azure, per illustrare le funzionalità di ripristino di emergenza di Azure Site Recovery per SAP. Poiché la replica di Azure Site Recovery è indipendente dall'applicazione, il processo descritto è valido anche per gli altri scenari.

### <a name="required-foundation-services"></a>Servizi di base necessari
Lo scenario descritto in questa documentazione è stato distribuito con i servizi di base seguenti:
* ExpressRoute o VPN (rete privata virtuale) da sito a sito
* Almeno un controller di dominio Active Directory e il server DNS in esecuzione in Azure

È consigliabile implementare l'infrastruttura illustrata in precedenza prima di distribuire Azure Site Recovery.


## <a name="typical-sap-application-deployment"></a>Distribuzione tipica di un'applicazione SAP
I clienti SAP di grandi dimensioni in genere distribuiscono tra 6 e 20 singole applicazioni SAP.  La maggior parte di queste applicazioni si basa su motori SAP NetWeaver ABAP o Java.  A supporto di queste applicazioni NetWeaver di base ci sono numerosi motori autonomi SAP non NetWeaver più piccoli e in genere alcune applicazioni non SAP.  

È fondamentale inventariare tutte le applicazioni SAP in esecuzione in un ambiente e determinare la modalità di distribuzione (a 2 o a 3 livelli), le versioni, le patch, la frequenza di varianza e i requisiti di persistenza del disco.

![Modello di distribuzione](./media/site-recovery-sap/sap-typical-deployment.png)

Il livello di persistenza del database SAP deve essere protetto tramite strumenti DBMS nativi, ad esempio SQL Server AlwaysOn, Oracle DataGuard o la replica di sistema HANA. Il livello client non è protetto da Azure Site Recovery, ma è importante prendere in considerazione gli argomenti che influiscono su questo livello, ad esempio il ritardo della propagazione DNS, la sicurezza e l'accesso remoto al data center di ripristino di emergenza.

Azure Site Recovery è la soluzione consigliata per il livello dell'applicazione, incluso (A)SCS. Altre applicazioni, ad esempio le applicazioni SAP non NetWeaver e le applicazioni non SAP fanno parte dell'ambiente di distribuzione SAP globale e devono essere anch'esse protette con Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Replicare le macchine virtuali
Seguire [queste linee guida](azure-to-azure-walkthrough-enable-replication.md) per avviare la replica di tutte le macchine virtuali delle applicazioni SAP nel data center di ripristino di emergenza di Azure.

Se si usa un indirizzo IP statico, è possibile specificare l'indirizzo IP per la macchina virtuale nella sezione relativa alle schede di interfaccia di rete delle impostazioni Calcolo e rete.

![IP di destinazione](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Creazione di un piano di ripristino
Un piano di ripristino consente di definire la sequenza di failover di vari livelli in un'applicazione multilivello, conservando la coerenza dell'applicazione. Seguire la procedura descritta [qui](site-recovery-create-recovery-plans.md) durante la creazione di un piano di ripristino per un'applicazione Web multilivello.

### <a name="adding-scripts-to-the-recovery-plan"></a>Aggiunta di script al piano di ripristino
Potrebbe essere necessario eseguire alcune operazioni nelle macchine virtuali di Azure dopo il failover o il failover di test per garantire il corretto funzionamento delle applicazioni. È possibile automatizzare le operazioni successive al failover, come l'aggiornamento della voce DNS e la modifica dei binding e delle connessioni, aggiungendo gli script corrispondenti nel piano di ripristino, come illustrato in [questo articolo](site-recovery-how-to-add-vmmscript.md).

### <a name="dns-update"></a>Aggiornamento del DNS
Se il DNS è configurato per l'aggiornamento DNS dinamico, le macchine virtuali aggiornano in genere il DNS con il nuovo indirizzo IP all'avvio. Se si vuole aggiungere un passaggio esplicito per l'aggiornamento del DNS con i nuovi indirizzi IP delle macchine virtuali, aggiungere questo [script per l'aggiornamento degli indirizzi IP nel DNS](https://aka.ms/asr-dns-update) come post-azione nei gruppi del piano di ripristino.  

## <a name="example-azure-to-azure-deployment"></a>Esempio di distribuzione da Azure ad Azure
Nella figura seguente è illustrato lo scenario di ripristino di emergenza da Azure ad Azure di Azure Site Recovery:
* Il data center principale è a Singapore (area Asia sud-orientale di Azure) e il data center di ripristino di emergenza è a Hong Kong (area Asia orientale di Azure).  In questo scenario, la disponibilità elevata locale viene fornita con due VM che eseguono SQL Server AlwaysOn in modalità sincrona a Singapore.
* È possibile usare l'istanza ASCS di condivisione file per fornire disponibilità elevata per i singoli punti di errore SAP. L'istanza ASCS di condivisione file non richiede un disco condiviso del cluster e le applicazioni, ad esempio SIOS, non sono necessarie.
* La protezione tramite ripristino di emergenza per il livello DBMS viene ottenuta mediante la replica asincrona.
* Questo scenario illustra il "ripristino di emergenza simmetrico", ovvero una soluzione di ripristino di emergenza che è una replica esatta dell'ambiente di produzione, quindi la soluzione di ripristino di emergenza di SQL Server ha disponibilità elevata locale. L'uso del ripristino di emergenza simmetrico non è obbligatorio per il livello di database e molti clienti sfruttano la flessibilità delle distribuzioni cloud per creare rapidamente un nodo a disponibilità elevata locale dopo un evento di ripristino di emergenza.
* Il diagramma illustra i livelli del server applicazioni e ASCS di SAP NetWeaver replicati da Azure Site Recovery.

![Scenario di replica](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>Esecuzione di un failover di test
Seguire [queste linee guida](azure-to-azure-walkthrough-test-failover.md) per eseguire un failover di test.

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Fare clic sul piano di ripristino creato per l'applicazione o le applicazioni SAP.
3.  Fare clic su 'Failover di test'.
4.  Selezionare il punto di recupero e la rete virtuale di Azure per avviare il processo di failover di test.
5.  Quando l'ambiente secondario è disponibile, è possibile eseguire le convalide.
6.  Al termine delle convalide, fare clic sul pulsante per la pulizia del failover di test per pulire l'ambiente di failover.

## <a name="doing-a-failover"></a>Esecuzione di un failover
Seguire [queste linee guida](site-recovery-failover.md) quando si esegue un failover.

1.  Accedere al portale di Azure e selezionare l'insieme di credenziali di Servizi di ripristino.
2.  Fare clic sul piano di ripristino creato per l'applicazione o le applicazioni SAP.
3.  Fare clic su 'Failover'.
4.  Selezionare il punto di recupero per avviare il processo di failover.

## <a name="next-steps"></a>Passaggi successivi
Leggere altre informazioni sulla creazione di una soluzione di ripristino di emergenza per le distribuzioni SAP NetWeaver con Azure Site Recovery in [questo white paper](http://aka.ms/asr-sap). Il white paper include anche consigli per diverse architetture SAP, elenca le applicazioni e i tipi di VM supportati per SAP in Azure e descrive i piani di test possibili per la soluzione di ripristino di emergenza.

Leggere altre informazioni sulla [replica di altri carichi di lavoro](site-recovery-workload.md) con Site Recovery.
