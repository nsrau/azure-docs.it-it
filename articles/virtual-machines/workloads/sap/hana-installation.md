---
title: Installare SAP HANA su SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft
description: Come installare SAP HANA in SAP HANA in Azure (istanza di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 0d998b5347a9da6dd209b0ba106792ab5c34164d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure

L'installazione di SAP HANA è responsabilità dell'utente e può essere eseguita immediatamente dopo la consegna di un nuovo SAP HANA su server di Azure (istanze di grandi dimensioni). Si noti che, in base ai criteri SAP, l'installazione di SAP HANA deve essere eseguita da un installatore certificato SAP HANA, ovvero un utente che ha superato l'esame di certificazione Certified SAP Technology Associate – SAP HANA Installation, o da un integratore di sistemi (SI) SAP.

Ci sono alcune considerazioni specifiche sulla connettività di SAP HANA (lato server) e SAP HANA (lato client) degne di nota. In molti casi il server SAP HANA invia il suo indirizzo IP al client in cui viene memorizzato nella cache e utilizzato per i successivi tentativi di connessione. Perché SAP HANA in Azure (istanze di grandi dimensioni) esegue il NAT sull'indirizzo IP interno del server utilizzato nella rete tenant verso un intervallo di indirizzi IP fornito per reti virtuali di Azure specificate, il server di database SAP HANA, per impostazione predefinita, invia l'intervallo di indirizzi IP &quot;interno&quot;. Ad esempio, per la risoluzione dei nomi host, invece di fare in modo che SAP HANA fornisca l'indirizzo IP su cui è stato eseguito il NAT, viene utilizzato l'indirizzo IP interno memorizzato nella cache. Perciò un'applicazione che usa un client SAP HANA (ODBC, JDBC e così via) non è in grado di connettersi con questo indirizzo IP. Per indicare al server SAP HANA che è necessario propagare l'indirizzo IP su cui è stato eseguito il NAT al client, è necessario modificare il file di configurazione di sistema globale di SAP HANA (global.ini).

Aggiungere il codice seguente al file global.ini (direttamente o tramite SAP HANA Studio):
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
Per altri dettagli ed esempi, vedere la sezione _Mapping Host Names for Database Client Access_ (Mapping dei nomi di host per l'accesso ai client di database) in [SAP HANA Administration Guide](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf) (Guida all'amministrazione di SAP HANA).

Sul lato client (per i server applicazioni SAP in Azure), modificare il file /etc/hosts file (in Linux) o /system32/drivers/etc/hosts (in Windows Server) e includere una voce per il name host di SAP HANA nel tenant di Azure (istanze di grandi dimensioni) e i rispettivi indirizzi IP su cui è stato eseguito il NAT.

>[!NOTE] 
>Quando si installa il client di database SAP HANA, se si verificano errori in fase di connessione a SAP HANA in Azure (istanze di grandi dimensioni) durante l'installazione del client di database SAP HANA, usare l'indirizzo IP NAT del tenant delle istanze HANA di grandi dimensioni implicitamente anziché il nome host.

## <a name="storage"></a>Archiviazione

Il layout di archiviazione per SAP HANA in Azure (istanze di grandi dimensioni) viene configurato da SAP HANA in Azure Service Management tramite procedure consigliate SAP: vedere il white paper sui [requisiti di archiviazione di SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Le configurazioni esatte della risorsa di archiviazione per le diverse istanze HANA di grandi dimensioni sono simili a queste:

| Dimensione della memoria | HANA/data | HANA/log | HANA/shared | HANA/log/backups |
| --- | --- | --- | --- | --- |
| 768 GB | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1536 GB | 3456 GB | 768 GB | 1024 GB | 768 GB |
| 3072 GB | 7680 GB | 1536 GB | 1024 GB | 1536 GB |

Inoltre, i clienti possono acquistare capacità di archiviazione aggiuntiva per incrementi di 1 TB. Questo ulteriore spazio di archiviazione può essere aggiunto come nuovi volumi alle istanze HANA di grandi dimensioni.

Il controller di archiviazione e i nodi nei timestamp dell'istanza di grandi dimensioni sono sincronizzati con i server NTP. Poiché si sincronizza SAP HANA nelle unità Azure (istanze di grandi dimensioni) e le VM di Azure in un server NTP, non si dovrebbero verificare sfasamenti di orario significativi tra l'infrastruttura e le unità di calcolo nei timestamp di Azure o dell'istanza di grandi dimensioni.

## <a name="operating-system"></a>Sistema operativo

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) è la distribuzione di Linux installata per SAP HANA in Azure (istanze di grandi dimensioni). Questa particolare distribuzione offre funzionalità specifiche di SAP &quot;predefinite&quot; (inclusi parametri preimpostati per l'esecuzione efficace si SAP in SLES).

Vedere il [white paper sulle librerie di risorse](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) sul sito Web di SUSE e le informazioni relative a [SAP in SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) su SAP Community Network (SCN) per varie risorse utili relative alla distribuzione di SAP HANA in SLES (inclusa la configurazione di elevata disponibilità, la protezione avanzata specifica per le operazioni di SAP e altro ancora).

Utili collegamenti aggiuntivi riguardanti SLES:

- [Sito relativo a SAP HANA in SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Procedure consigliate per SAP: replica di accodamento e SAP NetWeaver in SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP: SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (incluso SLES 12 for SAP Applications)

Note di supporto SAP applicabili all'implementazione di SAP HANA su SLES 12 SP1:

- [Nota di supporto SAP #1944799: linee guida di SAP HANA per l'installazione del sistema operativo SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [Nota di supporto SAP #2205917: impostazioni del sistema operativo consigliate per il database di SAP HANA per SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E)
- [Nota di supporto SAP #1984787: note di installazione per SUSE Linux Enterprise Server 12](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota di supporto SAP #171356: informazioni generali sul software SAP in Linux](https://launchpad.support.sap.com/#/notes/1984787)
- [Nota di supporto SAP #1391070: soluzioni UUID Linux](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>Sincronizzazione degli orari

SAP è molto sensibili alle differenze di orario per i vari componenti che costituiscono il sistema SAP. I dump SAP ABAP brevi con il titolo di errore ZDATE\_LARGE\_TIME\_DIFF sono probabilmente familiari per chi ha lavorato a lungo con SAP (base), in quanto questi dump brevi compaiono quando l'ora di sistema di server o VM differenti è troppo sfasata.

Per SAP HANA in Azure (istanze di grandi dimensioni), la sincronizzazione dell'ora eseguita in Azure non si applica alle unità di calcolo nei timestamp dell'istanza di grandi dimensioni. Questo non vale per l'esecuzione delle applicazioni SAP in modo nativo in Azure (nelle VM), poiché Azure assicura la corretta sincronizzazione dell'ora del sistema. Di conseguenza è necessario configurare un server di riferimento ora separato che possa essere utilizzato dai server applicazioni SAP in esecuzione nelle VM di Azure e dalle istanze di database SAP HANA in esecuzione nelle istanze HANA di grandi dimensioni. L'ora dell'infrastruttura di archiviazione nei timestamp dell'istanza di grandi dimensioni è sincronizzata con i server NTP.



