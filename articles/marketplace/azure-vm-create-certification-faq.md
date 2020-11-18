---
title: Risoluzione dei problemi di certificazione della macchina virtuale (VM) per Azure Marketplace
description: Risolvere i problemi comuni relativi al test e alla certificazione delle immagini di macchine virtuali (VM) per Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 5124e7084a8e878e54e594424ec0926410f002ca
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682486"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Risolvere i problemi di certificazione della macchina virtuale

Quando si pubblica l'immagine della macchina virtuale (VM) in Azure Marketplace, il team di Azure la convalida per assicurarsi che sia avviabile, sicura e compatibile con Azure. Se l'immagine della macchina virtuale ha esito negativo, non verrà pubblicata. Verrà visualizzato un messaggio di errore in cui viene descritto il problema.

Questo articolo illustra i messaggi di errore comuni durante la pubblicazione di immagini di VM, insieme alle soluzioni correlate.

> [!NOTE]
> Per domande su questo articolo o suggerimenti per migliorare, contattare il [supporto tecnico del centro](https://aka.ms/marketplacepublishersupport)per i partner.

## <a name="approved-base-image"></a>Immagine di base approvata

Quando si invia una richiesta per ripubblicare l'immagine con gli aggiornamenti, la verifica del numero di parte test case potrebbe non riuscire. In caso di errore, l'immagine non verrà approvata.

Questo errore si verifica quando si usa un'immagine di base che appartiene a un altro server di pubblicazione ed è stata aggiornata l'immagine. In questa situazione non sarà consentito pubblicare l'immagine.

Per risolvere questo problema, recuperare l'immagine da Azure Marketplace e apportare le modifiche. Per altre informazioni, vedere gli articoli seguenti:

- [Immagini di Linux](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Immagini di Windows](azure-vm-create-using-approved-base.md)

> [!Note]
> Se si usa un'immagine di base Linux non ricavata da Azure Marketplace, è possibile sfalsare la prima partizione di 2048 KB. Questo consente di usare lo spazio non formattato per l'aggiunta di nuove informazioni di fatturazione e consente ad Azure di procedere con la pubblicazione della VM in Azure Marketplace.  

## <a name="vm-extension-failure"></a>Errore di estensione della macchina virtuale

Verificare se l'immagine supporta le estensioni della macchina virtuale.

Per abilitare le estensioni della macchina virtuale:

1. Selezionare la VM Linux.
1. Passare a **impostazioni di diagnostica**.
1. Abilitare le matrici di base aggiornando l' **account di archiviazione**.
1. Selezionare **Salva**.

   ![Screenshot che Mostra come abilitare il monitoraggio a livello di Guest.](./media/create-vm/vm-certification-issues-solutions-1.png)

Per verificare che le estensioni della macchina virtuale siano attivate correttamente:

1. Nella macchina virtuale selezionare la scheda **estensioni VM** e quindi verificare lo stato dell' **estensione di diagnostica per Linux**.
1. Verificare lo stato del provisioning.

   - Se lo stato è *provisioning riuscito*, il test case estensioni è stato superato.  
   - Se lo stato è *provisioning non riuscito*, le estensioni test case non sono riuscite ed è necessario impostare il flag finalizzato.

   ![Screenshot che mostra l'esito positivo del provisioning.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Se l'estensione della macchina virtuale ha esito negativo, vedere [usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../virtual-machines/extensions/diagnostics-linux.md) per abilitarla. Se non si vuole abilitare l'estensione della macchina virtuale, contattare il team di supporto e chiedere di disabilitarla.

## <a name="vm-provisioning-issue"></a>Problema di provisioning della macchina virtuale

Assicurarsi di aver seguito rigorosamente il processo di provisioning della macchina virtuale prima di inviare l'offerta. Per visualizzare il formato JSON per il provisioning della VM, vedere [testare un'immagine di macchina virtuale](azure-vm-image-test.md).

I problemi di provisioning possono includere gli scenari di errore seguenti:

|Scenario|Errore|Motivo|Soluzione|
|---|---|---|---|
|1|Disco rigido virtuale (VHD) non valido|Se il valore del cookie specificato nel piè di pagina del disco rigido virtuale non è corretto, il disco rigido virtuale verrà considerato non valido.|Ricreare l'immagine e inviare la richiesta.|
|2|Tipo di BLOB non valido|Il provisioning della macchina virtuale non è riuscito perché il blocco usato è un tipo di BLOB anziché un tipo di pagina.|Ricreare l'immagine e inviare la richiesta.|
|3|Timeout del provisioning o generalizzato in modo non corretto|Si è verificato un problema con la generalizzazione delle macchine virtuali.|Ricreare l'immagine con generalizzazione e inviare la richiesta.|

> [!NOTE]
> Per ulteriori informazioni sulla generalizzazione delle macchine virtuali, vedere:
> - [Documentazione di Linux](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Documentazione di Windows](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>Specifiche VHD

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Cookie Conectix e altre specifiche VHD

La stringa ' conectix ' fa parte della specifica del disco rigido virtuale. Viene definito come cookie a 8 byte nel piè di pagina VHD che identifica l'autore del file. Tutti i file VHD creati da Microsoft hanno questo cookie. 

Un BLOB formattato VHD deve avere un piè di pagina di 512 byte nel formato seguente:

|Campi del piè di pagina del disco rigido|Dimensioni (byte)|
|---|---|
Cookie|8
Funzionalità|4
Versione del formato di file|4
Offset dati|8
Timestamp|4
Applicazione Creator|4
Versione autore|4
Sistema operativo host Creator|4
Dimensioni originali|8
Dimensioni correnti|8
Geometria disco|4
Tipo di disco|4
Checksum|4
ID univoco|16
Stato salvato|1
Riservato|427


### <a name="vhd-specifications"></a>Specifiche VHD

Per garantire un'esperienza di pubblicazione uniforme, verificare che il disco rigido virtuale soddisfi i criteri seguenti:

- Il cookie contiene la stringa "conectix".
- Il tipo di disco è fisso.
- La dimensione virtuale del disco rigido virtuale è almeno 20 MB.
- Il disco rigido virtuale è allineato. La dimensione virtuale deve essere un multiplo di 1 MB.
- La lunghezza del BLOB VHD è uguale alla dimensione virtuale più la lunghezza del piè di pagina del disco rigido virtuale (512).

Scaricare la [specifica del disco rigido virtuale](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Conformità software per Windows

Se la richiesta di immagine di Windows viene rifiutata a causa di un problema di conformità del software, è possibile che sia stata creata un'immagine di Windows con un'istanza di SQL Server installata. È invece necessario utilizzare l'immagine di base della versione SQL Server pertinente da Azure Marketplace.

Non creare un'immagine di Windows personalizzata con SQL Server installato. Usare le immagini di base SQL Server approvate (Enterprise/standard/Web) da Azure Marketplace.

Se si sta tentando di installare Visual Studio o un prodotto concesso in licenza con Office, contattare il team di supporto per l'approvazione precedente.

Per ulteriori informazioni sulla selezione di una base approvata, vedere [creare una macchina virtuale da una base approvata](azure-vm-create-using-approved-base.md).

## <a name="toolkit-test-case-execution-failed"></a>Esecuzione del test case Toolkit non riuscita

Microsoft Certification Toolkit può essere utile per eseguire i test case e verificare che il disco rigido virtuale o l'immagine siano compatibili con l'ambiente Azure.

Scaricare [Microsoft Certification Toolkit](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Test case di Linux

La tabella seguente elenca i test case di Linux che verranno eseguiti dal Toolkit. La convalida dei test è indicata nella descrizione.

|Scenario|Test case|Descrizione|
|---|---|---|
|1|Cronologia bash|I file di cronologia bash devono essere cancellati prima di creare l'immagine di macchina virtuale.|
|2|Versione dell'agente Linux|È necessario installare l'agente Linux di Azure 2.2.41 o versione successiva.|
|3|Parametri del kernel richiesti|Verifica che siano impostati i seguenti parametri del kernel: <br>Console = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|Scambia partizione nel disco del sistema operativo|Verifica che le partizioni di scambio non vengano create nel disco del sistema operativo.|
|5|Partizione radice nel disco del sistema operativo|Creare una singola partizione radice per il disco del sistema operativo.|
|6|Versione OpenSSL|La versione di OpenSSL dovrebbe essere v 0.9.8 o successiva.|
|7|Versione Python|È consigliabile usare Python versione 2,6 o successiva.|
|8|Intervallo di client attivo|Impostare ClientAliveInterval su 180. Per quanto necessario, l'applicazione può essere impostata da 30 a 235. Se si Abilita SSH per gli utenti finali, questo valore deve essere impostato come illustrato.|
|9|Architettura del sistema operativo|Sono supportati solo i sistemi operativi a 64 bit.|
|10|Aggiornamento automatico|Indica se l'aggiornamento automatico dell'agente Linux è abilitato.|

### <a name="common-test-case-errors"></a>Errori comuni di test case

Fare riferimento alla tabella seguente per gli errori comuni che possono essere visualizzati durante l'esecuzione di test case:

| Scenario | Test case | Errore | Soluzione |
| --- | --- | --- | --- |
| 1 | Versione dell'agente Linux test case | La versione minima dell'agente Linux è 2.2.41 o successiva. Questo requisito è stato obbligatorio a partire dal 1 ° maggio 2020. | Aggiornare la versione dell'agente Linux. Deve essere 2,241 o versione successiva. Per ulteriori informazioni, vedere la pagina relativa all' [aggiornamento della versione dell'agente Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support). |
| 2 | Cronologia bash test case | Si verifica un errore se la dimensione della cronologia bash nell'immagine inviata è superiore a 1 kilobyte (KB). La dimensione è limitata a 1 KB per garantire che il file di cronologia bash non contenga informazioni potenzialmente riservate. | Risolvere montando il disco rigido virtuale in un'altra VM funzionante e apportare modifiche per ridurre le dimensioni a 1 KB o meno. Ad esempio, eliminare i `.bash` file della cronologia. |
| 3 | Parametro kernel obbligatorio test case | Questo errore viene visualizzato quando il valore di `console` non è impostato su `ttyS0` . Verificare eseguendo il comando seguente: <br /> `cat /proc/cmdline` | Impostare il valore per `console` su `ttyS0` e inviare nuovamente la richiesta. |
| 4 | test case intervallo ClientAlive | Se il Toolkit restituisce un risultato non riuscito per questo test case, esiste un valore non appropriato per `ClientAliveInterval` . | Impostare il valore per `ClientAliveInterval` su un valore minore o uguale a 235, quindi inviare nuovamente la richiesta. |


### <a name="windows-test-cases"></a>Test case di Windows

Nella tabella seguente sono elencati i test case di Windows che verranno eseguiti dal Toolkit, insieme a una descrizione della convalida dei test:

|Scenario |Test case|Descrizione|
|---|---|---|---|
|1|Architettura del sistema operativo|Azure supporta solo i sistemi operativi a 64 bit.|
|2|Dipendenza account utente|L'esecuzione dell'applicazione non deve dipendere dall'account Administrator.|
|3|Cluster di failover|La funzionalità clustering di failover di Windows Server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|4|IPV6|IPv6 non è ancora supportato nell'ambiente Azure. L'applicazione non deve dipendere da questa funzionalità.|
|5|DHCP|Il ruolo del server Dynamic Host Configuration Protocol non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|6|Hyper-V|Il ruolo del server Hyper-V non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|7|Accesso remoto|Il ruolo del server accesso remoto (accesso diretto) non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|8|Rights Management Services|Servizi Rights Management. Il ruolo del server non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|9|Servizi di distribuzione Windows|Servizi di distribuzione Windows. Il ruolo del server non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|10|Crittografia unità BitLocker|Crittografia unità BitLocker non è supportato nel disco rigido del sistema operativo, ma può essere usato nei dischi dati.|
|11|Internet Storage Name Server|La funzionalità del server dei nomi di archiviazione Internet non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|12|Multipath I/O|Multipath I/O. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|13|Bilanciamento carico di rete|Bilanciamento carico di rete. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|14|Protocollo PNRP (Peer Name Resolution Protocol)|Protocollo di risoluzione del nome peer. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|15|Servizi SNMP|La funzionalità Servizi di Simple Network Management Protocol (SNMP) non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|16|Windows Internet Name Service|Windows Internet Name Service. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|17|Servizio LAN Wireless|Servizio LAN wireless. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|

Se vengono rilevati errori con i test case precedenti, fare riferimento alla colonna **Descrizione** nella tabella per la soluzione. Per ulteriori informazioni, contattare il team di supporto. 

## <a name="data-disk-size-verification"></a>Verifica delle dimensioni del disco dati

Le richieste del disco dati con dimensioni maggiori di 1023 gigabyte (GB) non verranno approvate. Questa regola si applica sia a Linux che a Windows.

Inviare nuovamente la richiesta con una dimensione minore o uguale a 1023 GB.

## <a name="os-disk-size-validation"></a>Convalida delle dimensioni del disco del sistema operativo

Per le limitazioni sulle dimensioni del disco del sistema operativo, fare riferimento alle regole seguenti. Quando si invia una richiesta, verificare che le dimensioni del disco del sistema operativo siano comprese nel limite per Linux o Windows.

|Sistema operativo|Dimensioni del disco rigido virtuale consigliate|
|---|---|
|Linux|da 30 GB a 1023 GB|
|Windows|da 30 GB a 250 GB|

Poiché le macchine virtuali consentono l'accesso al sistema operativo sottostante, verificare che le dimensioni del disco rigido virtuale siano sufficientemente grandi per il disco rigido virtuale. I dischi non sono espandibili senza tempi di inattività. Usare una dimensione del disco da 30 GB a 50 GB.

|Dimensioni VHD|Dimensioni effettive occupate|Soluzione|
|---|---|---|
|>500 TB (TiB)|n/d|Contattare il team di supporto per l'approvazione di un'eccezione.|
|250-500 TiB|Differenza >200 gibibyte (GiB) dalle dimensioni del BLOB|Contattare il team di supporto per l'approvazione di un'eccezione.|

> [!NOTE]
> Dimensioni maggiori del disco comportano costi maggiori e comporteranno un ritardo durante il processo di installazione e di replica. A causa di questo ritardo e costo, il team di supporto potrebbe cercare di giustificare l'approvazione dell'eccezione.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test di verifica patch WannaCry per Windows

Per evitare un potenziale attacco correlato al virus WannaCry, verificare che tutte le richieste di immagini Windows vengano aggiornate con la patch più recente.

È possibile verificare la versione del file di immagine da `C:\windows\system32\drivers\srv.sys` o `srv2.sys` .

La tabella seguente illustra la versione minima con patch di Windows Server: 

|Sistema operativo|Versione|
|---|---|
|Windows serve 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|N/D|

> [!NOTE]
> Windows Server 2019 non presenta requisiti di versione obbligatori.

## <a name="sack-vulnerability-patch-verification"></a>Verifica della patch della vulnerabilità di SACK

Quando si invia un'immagine Linux, la richiesta potrebbe essere rifiutata a causa di problemi di versione del kernel.

Aggiornare il kernel con una versione approvata e inviare nuovamente la richiesta. È possibile trovare la versione del kernel approvata nella tabella seguente. Il numero di versione deve essere maggiore o uguale al numero elencato qui.

Se l'immagine non è installata con una delle seguenti versioni del kernel, aggiornarla con le patch corrette. Richiedere l'approvazione necessaria al team di supporto dopo che l'immagine è stata aggiornata con le patch richieste seguenti:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Famiglia del sistema operativo|Versione|Kernel|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0-151| 
||14.04 LTS|4.15.0-1049-*-Azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19,04|5.0.0-1010|
||19,04|5.3.0-1004|
|Sistema operativo RHEL e cent|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|TBD|
||RHEL-SAP 7,5|TBD|
|SLES|SLES11SP4 (incluso SAP)|3.0.101-108.95.2|
||SLES12SP1 per SAP|3.12.74-60.64.115.1|
||SLES12SP2 per SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 per SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 per SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 per SAP|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK segue RHEL|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|2079.6.0 stabile CoreOS|4.19.43*|
||2135.3.1 beta|4.19.50*|
||2163.2.1 alfa|4.19.50*|
|Debian|Jessie (sicurezza)|3.16.68-2|
||backport Jessie|4.9.168-1 + deb9u3|
||Stretch (sicurezza)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, SID (backports stretch)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>Le dimensioni dell'immagine devono essere in multipli di megabyte

Tutti i dischi rigidi virtuali in Azure devono avere una dimensione virtuale allineata a multipli di 1 megabyte (MB). Se il disco rigido virtuale non rispetta le dimensioni virtuali consigliate, è possibile che la richiesta venga rifiutata.

Seguire le linee guida quando si esegue la conversione da un disco non elaborato al disco rigido virtuale. Verificare che le dimensioni del disco non elaborato siano un multiplo di 1 MB. Per ulteriori informazioni, vedere [informazioni per le distribuzioni non approvate](../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Accesso alla VM negato

Un problema di _accesso negato_ per l'esecuzione di un test case nella macchina virtuale potrebbe essere causato da privilegi insufficienti.

Verificare di aver abilitato l'accesso appropriato per l'account in cui sono in esecuzione i test case autonomi. Abilitare l'accesso per l'esecuzione di test case, se non è abilitato. Se non si vuole abilitare l'accesso, è possibile condividere i risultati della test case automatica con il team di supporto.

Per inviare la richiesta con l'immagine SSH disabilitata per il processo di certificazione:

1. Eseguire lo [strumento di test di certificazione più recente per le macchine virtuali di Azure](https://aka.ms/AzureCertificationTestTool) nell'immagine.

2. Genera un [ticket di supporto](https://aka.ms/marketplacepublishersupport). Assicurarsi di alleghi il report Toolkit e fornire i dettagli dell'offerta:
   - Nome offerta
   - Nome entità di pubblicazione
   - ID piano/SKU e versione

3. Inviare nuovamente la richiesta di certificazione.

## <a name="download-failure"></a>Errore di download
    
Vedere la tabella seguente per eventuali problemi che si verificano quando si scarica l'immagine di macchina virtuale con un URL di firma di accesso condiviso (SAS).

|Scenario|Errore|Motivo|Soluzione|
|---|---|---|---|
|1|BLOB non trovato|Il disco rigido virtuale può essere eliminato o spostato dalla posizione specificata.|| 
|2|BLOB in uso|Il disco rigido virtuale viene usato da un altro processo interno.|Il disco rigido virtuale deve trovarsi nello stato usato quando lo si scarica con un URL di firma di accesso condiviso.|
|3|URL SAS non valido|L'URL SAS associato per il disco rigido virtuale non è corretto.|Ottenere l'URL SAS corretto.|
|4|Firma non valida|L'URL SAS associato per il disco rigido virtuale non è corretto.|Ottenere l'URL SAS corretto.|
|6|Intestazione condizionale HTTP|L'URL SAS non è valido.|Ottenere l'URL SAS corretto.|
|7|Nome VHD non valido|Verificare che esistano caratteri speciali, ad esempio un segno di percentuale `%` o virgolette `"` , nel nome del disco rigido virtuale.|Rinominare il file VHD rimuovendo i caratteri speciali.|

## <a name="first-mb-2048-kb-partition-linux-only"></a>Prima partizione MB (2048 KB) (solo Linux)

Quando si invia il disco rigido virtuale, assicurarsi che i primi 2048 KB del disco rigido virtuale siano vuoti. In caso contrario, la richiesta avrà esito negativo.

>[!NOTE]
>Per alcune immagini speciali, ad esempio quelle basate sulle immagini di base di Windows di Azure ricavate da Azure Marketplace, viene verificata la presenza di un tag di fatturazione e viene ignorata la partizione MB se il tag di fatturazione è presente e corrisponde ai valori interni disponibili.

### <a name="create-a-first-mb-2048-kb-partition-on-an-empty-vhd"></a>Creare una partizione di prima MB (2048 KB) in un disco rigido virtuale vuoto

Questi passaggi si applicano solo a Linux.

1. Creare qualsiasi tipo di macchina virtuale Linux, ad esempio Ubuntu, Cent OS o altro. Compilare i campi obbligatori e selezionare **Avanti: dischi >**.

   ![Screenshot che mostra la pagina creare una macchina virtuale con il pulsante "Next: disks Command" evidenziato.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Creare un disco non gestito per la macchina virtuale.

   Usare i valori predefiniti o specificare qualsiasi valore per campi come NIC, NSG e IP pubblico.

   ![Immagine della schermata della pagina "dischi dati" nel creare un flusso della macchina virtuale.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Dopo aver creato la VM, selezionare **dischi** nel riquadro sinistro.

   ![Screenshot che illustra come selezionare i dischi per una macchina virtuale.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Alleghi il disco rigido virtuale come disco dati alla macchina virtuale per la creazione di una tabella di partizione.

   1. Selezionare **Aggiungi**  >  **BLOB esistente** DataDisk.

      ![Screenshot che illustra come aggiungere un disco dati al VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Trovare l'account di archiviazione VHD.
   1. Selezionare **contenitore** e quindi selezionare il disco rigido virtuale.
   1. Selezionare **OK**.

      ![Screenshot della pagina Connetti disco non gestito.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Il disco rigido virtuale verrà aggiunto come LUN 0 del disco dati.

   1. Riavviare la VM.

1. Dopo aver riavviato la macchina virtuale, accedere alla macchina virtuale usando putty o un altro client ed eseguire il `sudo  -i` comando per ottenere l'accesso alla radice.

   ![Screenshot della riga di comando del client putty che mostra il comando sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Creare una partizione nel disco rigido virtuale.

   1. `fdisk /dev/sdb`Comando Enter.
   1. Per visualizzare l'elenco delle partizioni esistente dal disco rigido virtuale, immettere `p` .
   1. Immettere `d` per eliminare tutte le partizioni esistenti disponibili nel disco rigido virtuale. È possibile ignorare questo passaggio, se non è obbligatorio.

      ![Schermata della riga di comando del client putty che mostra i comandi per eliminare le partizioni esistenti.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Immettere `n` per creare una nuova partizione e selezionare `p` per (partizione primaria).

   1. Immettere 2048 come _primo valore settore_ . È possibile lasciare l' _ultimo settore_ come valore predefinito.

      >[!IMPORTANT]
      >Tutti i dati esistenti verranno cancellati fino a 2048 KB. Backup del disco rigido virtuale prima di creare una nuova partizione.

      ![Schermata della riga di comando del client putty che mostra i comandi e l'output per i dati cancellati.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Digitare `w` per confermare la creazione della partizione. 

      ![Schermata della riga di comando del client putty che mostra i comandi per la creazione di una partizione.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. È possibile verificare la tabella di partizione eseguendo il comando `n fdisk /dev/sdb` e digitando `p` . Si noterà che la partizione viene creata con il valore di offset 2048. 

      ![Schermata della riga di comando del client putty che mostra i comandi per la creazione dell'offset 2048.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Scollegare il disco rigido virtuale dalla macchina virtuale ed eliminare la macchina virtuale.

### <a name="create-a-first-mb-2048-kb-partition-by-moving-existing-data-on-vhd"></a>Creare una partizione di prima MB (2048 KB) spostando i dati esistenti nel disco rigido virtuale

Questi passaggi si applicano solo a Linux.

1. Creare qualsiasi tipo di macchina virtuale Linux, ad esempio Ubuntu, Cent OS o altro. Compilare i campi obbligatori e selezionare **Avanti: dischi >**.

   ![Screenshot che mostra la pagina creare una macchina virtuale con il pulsante "Next: disks Command" evidenziato.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Creare un disco non gestito per la macchina virtuale.

   ![Immagine della schermata della pagina "dischi dati" nel creare un flusso della macchina virtuale.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Usare i valori predefiniti o specificare qualsiasi valore per campi come NIC, NSG e IP pubblico.

1. Dopo aver creato la VM, selezionare **dischi** nel riquadro sinistro.

   ![Screenshot che illustra come selezionare i dischi per una macchina virtuale.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Alleghi il disco rigido virtuale come disco dati alla macchina virtuale per la creazione di una tabella di partizione.

   1. Alleghi il disco rigido virtuale come disco dati alla macchina virtuale per la creazione di una tabella di partizione.

   1. Selezionare **Aggiungi**  >  **BLOB esistente** DataDisk.

      ![Screenshot che illustra come aggiungere un disco dati al VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Trovare l'account di archiviazione VHD.
   1. Selezionare **contenitore** e quindi selezionare il disco rigido virtuale.
   1. Selezionare **OK**.

      ![Screenshot della pagina Connetti disco non gestito.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Il disco rigido virtuale verrà aggiunto come LUN 0 del disco dati.

   1. Riavviare la VM.

1. Accedere alla VM con Putty o un altro client ed eseguire il `sudo  -i` comando per ottenere l'accesso alla radice.

   ![Screenshot della riga di comando del client putty che mostra l'accesso e il comando sudo-i.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Eseguire il comando `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1`.

   ![Schermata della riga di comando del client putty che mostra l'esecuzione dei comandi.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Il completamento di questo comando può richiedere del tempo perché dipende dalle dimensioni del disco.

1. Scollegare il disco rigido virtuale dalla macchina virtuale ed eliminare la macchina virtuale.


## <a name="default-credentials"></a>Credenziali predefinite

Non inviare mai credenziali predefinite con il VHD inviato. L'aggiunta di credenziali predefinite rende il disco rigido virtuale più vulnerabile alle minacce per la sicurezza. Al contrario, creare credenziali personalizzate quando si invia il disco rigido virtuale.
  
## <a name="datadisk-mapped-incorrectly"></a>Mapping di DataDisk non corretto

Un problema di mapping può verificarsi quando una richiesta viene inviata con più dischi dati che non sono in sequenza. Ad esempio, l'ordine di numerazione per tre dischi dati deve essere *0, 1, 2*. Qualsiasi altro ordine viene considerato un problema di mapping.

Inviare nuovamente la richiesta con la sequenziazione corretta dei dischi dati.

## <a name="incorrect-os-mapping"></a>Mapping del sistema operativo errato

Quando viene creata un'immagine, è possibile che venga eseguito il mapping a o che l'etichetta del sistema operativo non sia corretta. Ad esempio, quando si seleziona **Windows** come parte del nome del sistema operativo durante la creazione dell'immagine, il disco del sistema operativo deve essere installato solo con Windows. Lo stesso requisito si applica a Linux.

## <a name="vm-not-generalized"></a>VM non generalizzata

Se tutte le immagini tratte da Azure Marketplace devono essere riutilizzate, il disco rigido virtuale del sistema operativo deve essere generalizzato.

* Per **Linux**, il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata.

  Nella finestra SSH digitare il comando seguente: `sudo waagent -deprovision+user`.

* Per **Windows**, le immagini di Windows vengono generalizzate mediante `sysreptool` .

  Per ulteriori informazioni sullo `sysreptool` strumento, vedere [Cenni preliminari sulla preparazione del sistema (Sysprep)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Errori DataDisk

Per le soluzioni a errori correlati al disco dati, usare la tabella seguente:

|Errore|Motivo|Soluzione|
|---|---|---|
|`DataDisk- InvalidUrl:`|Questo errore può verificarsi a causa di un numero di unità logica (LUN) non valido quando l'offerta viene inviata.|Verificare che la sequenza di numeri LUN per il disco dati si trovi nel centro per i partner.|
|`DataDisk- NotFound:`|Questo errore può verificarsi perché un disco dati non si trova in corrispondenza di un URL SAS specificato.|Verificare che il disco dati si trovi nell'URL SAS specificato.|

## <a name="remote-access-issue"></a>Problema di accesso remoto

Questo errore viene ricevuto se l'opzione Remote Desktop Protocol (RDP) non è abilitata per l'immagine di Windows.

Abilitare l'accesso RDP per le immagini Windows prima di inviarli.

## <a name="bash-history-failed"></a>Cronologia bash non riuscita

Questo errore viene visualizzato se la dimensione della cronologia bash nell'immagine inviata è superiore a 1 kilobyte (KB). La dimensione è limitata a 1 KB per impedire che il file contenga informazioni potenzialmente riservate.

Per eliminare la cronologia bash:

1. Distribuire la macchina virtuale e selezionare l'opzione **Esegui comando** nella portale di Azure.

   ![Screenshot della portale di Azure con l'opzione ' Esegui comando ' nel riquadro sinistro.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Selezionare la prima opzione **RunShellScript** , quindi eseguire il comando: `cat /dev/null > ~/.bash_history && history -c` .

   ![Screenshot della pagina "Esegui script del comando" nel portale di Azure.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Quando il comando viene eseguito correttamente, riavviare la macchina virtuale.

1. Generalizzare la VM, estrarre il disco rigido virtuale dell'immagine e arrestare la macchina virtuale.

1. Inviare nuovamente l'immagine generalizzata.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Richiedi un'eccezione sulle immagini di macchina virtuale per i test selezionati

Gli editori possono richiedere eccezioni per alcuni test eseguiti durante la certificazione della macchina virtuale. Le eccezioni vengono fornite in rari casi quando un server di pubblicazione fornisce evidenza per supportare la richiesta. Il team di certificazione si riserva il diritto di negare o approvare le eccezioni in qualsiasi momento.

In questa sezione vengono descritti scenari generali in cui i Publisher richiedono un'eccezione e come richiederne uno.

### <a name="scenarios-for-exception"></a>Scenari per l'eccezione

In genere, i Publisher richiedono eccezioni nei casi seguenti:

- **Eccezione per uno o più test case**. Contattare il [supporto tecnico](https://aka.ms/marketplacepublishersupport) per richiedere eccezioni per i test case.

- **VM bloccate/nessun accesso radice**. Alcuni autori hanno scenari in cui le macchine virtuali devono essere bloccate perché hanno un software, ad esempio i firewall installati nella macchina virtuale. In tal caso, scaricare lo [strumento di test certificato](https://aka.ms/AzureCertificationTestTool) e inviare il report al [supporto tecnico di partner](https://aka.ms/marketplacepublishersupport).

- **Modelli personalizzati**. Alcuni editori pubblicano immagini di VM che richiedono un modello di Azure Resource Manager personalizzato (ARM) per distribuire le macchine virtuali. In questo caso, inviare i modelli personalizzati al [supporto tecnico del partner](https://aka.ms/marketplacepublishersupport) , in modo che possano essere usati dal team di certificazione per la convalida.

### <a name="information-to-provide-for-exception-scenarios"></a>Informazioni da fornire per gli scenari di eccezione

Contattare il [supporto tecnico](https://aka.ms/marketplacepublishersupport) per richiedere un'eccezione per uno degli scenari e includere le informazioni seguenti:

- **ID editore**. Digitare l'ID dell'editore del portale centrale partner.
- **ID/nome dell'offerta**. Immettere l'ID o il nome dell'offerta.
- **ID SKU/piano**. Digitare l'ID del piano di offerta della macchina virtuale o lo SKU.
- **Versione**. Immettere la versione dell'offerta di macchina virtuale che richiede un'eccezione.
- **Tipo di eccezione**. Scegliere tra i test, la macchina virtuale bloccata o i modelli personalizzati.
- **Motivo della richiesta**. Includere il motivo della richiesta di eccezione, oltre a tutte le informazioni sulle esenzioni del test.
- **Sequenza temporale**. Immettere la data di fine per l'eccezione.
- **Allegato**. Documenti di evidenza importanti allegati:

  - Per le VM bloccate, alleghi il report di test.
  - Per i modelli personalizzati, fornire il modello ARM personalizzato come allegato.

  Se non si includono questi allegati, la richiesta verrà negata.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Risolvere una vulnerabilità o un exploit in un'offerta di macchina virtuale

Questa sezione descrive come fornire una nuova immagine di macchina virtuale quando una vulnerabilità o un exploit viene individuato con una delle immagini di macchina virtuale. Si applica solo alle offerte di macchine virtuali di Azure pubblicate in Azure Marketplace.

> [!NOTE]
> Non è possibile rimuovere l'ultima immagine di macchina virtuale da un piano o arrestare la vendita dell'ultimo piano per un'offerta.

Eseguire una di queste azioni:

- Se si dispone di una nuova immagine di macchina virtuale per sostituire l'immagine di macchina virtuale vulnerabile, vedere [fornire un'immagine di macchina virtuale fissa](#provide-a-fixed-vm-image).
- Se non si ha una nuova immagine di macchina virtuale per sostituire l'unica immagine di macchina virtuale in un piano o se il piano è terminato, [interrompere la vendita del piano](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Se non si prevede di sostituire l'unica immagine di macchina virtuale nell'offerta, si consiglia di [smettere di vendere l'offerta](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Fornire un'immagine di macchina virtuale fissa

Per fornire un'immagine di macchina virtuale fissa per sostituire un'immagine di macchina virtuale con vulnerabilità o exploit:

1. Fornire una nuova immagine di macchina virtuale per risolvere le vulnerabilità o gli exploit di sicurezza.
1. Rimuovere l'immagine della macchina virtuale con la vulnerabilità o l'exploit di sicurezza.
1. Ripubblicare l'offerta.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Fornire una nuova immagine di macchina virtuale per risolvere le vulnerabilità della sicurezza o sfruttare gli exploit

Per completare questi passaggi, preparare le risorse tecniche per l'immagine di macchina virtuale che si vuole aggiungere. Per altre informazioni, vedere [creare una macchina virtuale usando una base approvata](azure-vm-create-using-approved-base.md)o [creare una macchina virtuale usando una propria immagine](azure-vm-create-using-own-image.md) e [generare un URI SAS per l'immagine di macchina](azure-vm-get-sas-uri.md)virtuale.

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
1. Nel riquadro a sinistra selezionare **Commercial Marketplace**  >  **Overview**.
1. Nella colonna **alias offerta** selezionare l'offerta.
1. Nella colonna **nome** della scheda **panoramica piano** selezionare il piano appropriato.
1. Nella scheda **configurazione tecnica** , in **Immagini VM**, selezionare **+ Aggiungi immagine macchina virtuale**.

   > [!NOTE]
   > È possibile aggiungere una sola immagine di macchina virtuale a un piano alla volta. Per aggiungere più immagini di VM, pubblicare la prima prima di aggiungere l'immagine di macchina virtuale successiva.

1. Nelle caselle visualizzate specificare una nuova versione del disco e l'immagine della macchina virtuale.
1. Selezionare **Salva bozza**.

Rimuovere quindi l'immagine della macchina virtuale con la vulnerabilità di sicurezza.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>Rimuovere l'immagine di macchina virtuale con vulnerabilità di sicurezza o exploit

1. Accedere al [Centro per i partner](https://partner.microsoft.com/dashboard/home).
2. Nel riquadro a sinistra selezionare **Commercial Marketplace**  >  **Overview**.
3. Nella colonna **alias offerta** selezionare l'offerta.
4. Nella colonna **nome** della scheda **panoramica piano** selezionare il piano appropriato.
5. Nella scheda **configurazione tecnica** , in **Immagini VM**, accanto all'immagine di macchina virtuale che si vuole rimuovere selezionare **Rimuovi immagine VM**.
6. Nella finestra di dialogo selezionare **continua**.
7. Selezionare **Salva bozza**.

Quindi, ripubblicare l'offerta.

#### <a name="republish-the-offer"></a>Ripubblicare l'offerta

1. Selezionare **revisione e pubblicazione**.
2. Se è necessario fornire informazioni al team di certificazione, aggiungerlo alla casella **Note per la certificazione** .
3. Selezionare **Pubblica**.

Per completare il processo di pubblicazione, vedere [rivedere e pubblicare le offerte](review-publish-offer.md).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le proprietà dell'offerta di VM](azure-vm-create-properties.md)
- [Vantaggi del Marketplace attivo](partner-center-portal/marketplace-rewards.md)
- In caso di domande o commenti e suggerimenti, contattare il [supporto tecnico del centro](https://aka.ms/marketplacepublishersupport)per i partner.
