---
title: Monitoraggio di Azure per i provider di soluzioni SAP | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su monitoraggio di Azure per i provider di soluzioni SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 056eba8694d1727350809121f763181e3cdbdc64
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968605"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Monitoraggio di Azure per i provider di soluzioni SAP (anteprima)

## <a name="overview"></a>Panoramica  

Nel contesto di monitoraggio di Azure per le soluzioni SAP, un *tipo di provider* si riferisce a un *provider* specifico. Ad esempio *SAP Hana*, configurato per un componente specifico all'interno del panorama applicativo SAP, come SAP Hana database. Un provider contiene le informazioni di connessione per il componente corrispondente e consente di raccogliere dati di telemetria da tale componente. Una risorsa di monitoraggio di Azure per SAP Solutions (nota anche come risorsa di monitoraggio SAP) può essere configurata con più provider dello stesso tipo di provider o più provider di più tipi di provider.
   
I clienti possono scegliere di configurare tipi di provider diversi per consentire la raccolta di dati dal componente corrispondente nel panorama applicativo SAP. I clienti possono ad esempio configurare un provider per il tipo di provider SAP HANA, un altro provider per il tipo di provider cluster a disponibilità elevata e così via.  

I clienti possono anche scegliere di configurare più provider di un tipo di provider specifico per riusare la stessa risorsa di monitoraggio SAP e il gruppo gestito associato. Altre informazioni sul gruppo di risorse gestite. Per l'anteprima pubblica sono supportati i tipi di provider seguenti:   
- SAP HANA
- Cluster a disponibilità elevata
- Microsoft SQL Server

![Monitoraggio di Azure per i provider di soluzioni SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Si consiglia ai clienti di configurare almeno un provider dai tipi di provider disponibili al momento della distribuzione della risorsa di monitoraggio SAP. Configurando un provider, i clienti avviano la raccolta dei dati dal componente corrispondente per il quale è configurato il provider.   

Se i clienti non configurano alcun provider al momento della distribuzione della risorsa di monitoraggio SAP, anche se la risorsa di monitoraggio SAP verrà distribuita correttamente, non verranno raccolti dati di telemetria. I clienti hanno la possibilità di aggiungere provider dopo la distribuzione tramite la risorsa di monitoraggio SAP all'interno portale di Azure. I clienti possono aggiungere o eliminare i provider dalla risorsa di monitoraggio SAP in qualsiasi momento.

> [!Tip]
> Se si vuole che Microsoft implementi un provider specifico, inviare commenti e suggerimenti tramite il collegamento alla fine di questo documento o contattare il team dell'account.  

## <a name="provider-type-sap-hana"></a>Tipo di provider SAP HANA

I clienti possono configurare uno o più provider di tipo *SAP Hana* per abilitare la raccolta di dati da SAP Hana database. Il provider di SAP HANA si connette al database di SAP HANA tramite la porta SQL, estrae i dati di telemetria dal database e li inserisce nell'area di lavoro Log Analytics della sottoscrizione del cliente. Il provider di SAP HANA raccoglie i dati ogni 1 minuto dal database SAP HANA.  

Nella versione di anteprima pubblica, i clienti possono prevedere di visualizzare i dati seguenti con SAP HANA provider: utilizzo dell'infrastruttura sottostante, SAP HANA stato dell'host, SAP HANA la replica del sistema e dati di telemetria del backup SAP HANA. Per configurare SAP HANA provider, l'indirizzo IP host, il numero di porta HANA SQL e il nome utente e la password SYSTEMDB sono obbligatori. Si consiglia ai clienti di configurare SAP HANA provider rispetto a SYSTEMDB, tuttavia è possibile configurare altri provider con altri tenant del database.

![Monitoraggio di Azure per i provider di soluzioni SAP-SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Tipo di provider cluster a disponibilità elevata
I clienti possono configurare uno o più provider di un *cluster a disponibilità elevata* di tipo provider per abilitare la raccolta di dati dal cluster Pacemaker all'interno del panorama applicativo SAP. Il provider di cluster a disponibilità elevata si connette a pacemaker, usando [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) endpoint, estrae i dati di telemetria dal database e li inserisce nell'area di lavoro log Analytics nella sottoscrizione del cliente. Il provider di cluster a disponibilità elevata raccoglie i dati ogni 60 secondi da pacemaker.  

Nella versione di anteprima pubblica, i clienti possono prevedere di visualizzare i dati seguenti con il provider di cluster a disponibilità elevata:   
 - Stato del cluster rappresentato come rollup dello stato dei nodi e delle risorse 
 - [altri](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Monitoraggio di Azure per i provider di soluzioni SAP-cluster a disponibilità elevata](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Per configurare un provider di cluster a disponibilità elevata, sono necessari due passaggi principali:

1. Installare [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *ogni* nodo all'interno del cluster pacemaker.

   Sono disponibili due opzioni per l'installazione di ha_cluster_exporter:
   
   - Usare gli script di automazione di Azure per distribuire un cluster a disponibilità elevata. Gli script installano [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in ogni nodo del cluster.  
   - Eseguire un' [installazione manuale](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Configurare un provider di cluster a disponibilità elevata per *ogni* nodo all'interno del cluster pacemaker.

   Per configurare il provider di cluster a disponibilità elevata, sono necessarie le informazioni seguenti:
   
   - **Nome**. Nome del provider. Deve essere univoco per questa istanza di monitoraggio di Azure per le soluzioni SAP.
   - **Endpoint Prometeo**. In genere http \: // \<servername or ip address\> : 9664/Metrics.
   - **SID**. Per i sistemi SAP, usare il SID SAP. Per gli altri sistemi, ad esempio i cluster NFS, usare un nome di tre caratteri per il cluster. Il SID deve essere diverso dagli altri cluster monitorati.   
   - **Nome del cluster**. Nome del cluster usato durante la creazione del cluster. Il nome del cluster si trova nella proprietà del cluster `cluster-name` .
   - **Nome host**. Nome host Linux della macchina virtuale.  

## <a name="provider-type-microsoft-sql-server"></a>Tipo di provider Microsoft SQL Server

I clienti possono configurare uno o più provider di tipo *Microsoft SQL Server* per abilitare la raccolta dati da [SQL Server in macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server provider si connette al Microsoft SQL Server sulla porta SQL, estrae i dati di telemetria dal database e li inserisce nell'area di lavoro Log Analytics della sottoscrizione del cliente. È necessario che il SQL Server sia configurato per l'autenticazione SQL e che sia stato creato un account di accesso SQL Server con il database SAP come database predefinito per il provider. SQL Server provider raccoglie i dati tra ogni 60 secondi fino a ogni ora da SQL Server.  

Nella versione di anteprima pubblica, i clienti possono prevedere di visualizzare i dati seguenti con SQL Server provider: l'utilizzo dell'infrastruttura sottostante, le prime istruzioni SQL, la tabella più grande, i problemi registrati nei log degli errori SQL Server, i processi di blocco e altri.  

Per configurare Microsoft SQL Server provider sono necessari l'ID del sistema SAP, l'indirizzo IP dell'host, il numero di porta SQL Server nonché il nome e la password dell'account di accesso SQL Server.

![Monitoraggio di Azure per i provider di soluzioni SAP-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima risorsa di monitoraggio di Azure per le soluzioni SAP.
- Hai domande su monitoraggio di Azure per le soluzioni SAP? Vedere la sezione [domande frequenti](./azure-monitor-faq.md)
