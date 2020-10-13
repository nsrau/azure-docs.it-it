---
title: Problemi comuni durante la certificazione delle immagini di macchine virtuali per Azure Marketplace
description: Questo articolo illustra i messaggi di errore e i problemi comuni durante il test e la certificazione delle immagini di VM per Azure Marketplace. Vengono inoltre illustrate le soluzioni correlate.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 06/16/2020
ms.openlocfilehash: d724ef463d7c7ad237b5fd023e9c15f50de96f04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803467"
---
# <a name="common-issues-when-certifying-virtual-machine-images-for-azure-marketplace"></a>Problemi comuni durante la certificazione delle immagini di macchine virtuali per Azure Marketplace

Quando si pubblica l'immagine della macchina virtuale (VM) in Azure Marketplace, il team di Azure la convalida per garantirne l'avvio, la sicurezza e la compatibilità con Azure. Se uno dei test di qualità elevata ha esito negativo, la pubblicazione avrà esito negativo e verrà visualizzato un messaggio di errore in cui viene descritto il problema.

Questo articolo illustra i messaggi di errore comuni durante la pubblicazione di immagini di VM, insieme alle soluzioni correlate.

> [!NOTE]
> Per domande o commenti e suggerimenti per il miglioramento, contattare il [supporto tecnico del centro](https://partner.microsoft.com/support/v2/?stage=1)per i partner.

## <a name="approved-base-image"></a>Immagine di base approvata

Quando si invia una richiesta per ripubblicare l'immagine con gli aggiornamenti, la verifica del numero di parte test case potrebbe non riuscire. In caso di errore, l'immagine non verrà approvata.

Questo errore si verifica quando si usa un'immagine di base che appartiene a un altro server di pubblicazione ed è stata aggiornata l'immagine. In questa situazione non sarà consentito pubblicare l'immagine.

Per risolvere questo problema, recuperare l'immagine da Azure Marketplace e apportare le modifiche. Per altre informazioni, vedere gli articoli seguenti:

- [Immagini di Linux](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Immagini di Windows](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)

> [!Note]
> Se si usa un'immagine di base Linux non ricavata da Marketplace, è possibile sfalsare la prima partizione di 2048 KB. Questo consente di usare lo spazio non formattato per l'aggiunta di nuove informazioni di fatturazione e consente ad Azure di procedere con la pubblicazione della VM nel Marketplace.  

## <a name="vm-extension-failure"></a>Errore di estensione della macchina virtuale

Verificare se l'immagine supporta le estensioni della macchina virtuale.

Per abilitare le estensioni della macchina virtuale, eseguire le operazioni seguenti:

1. Selezionare la VM Linux.
1. Passare a **impostazioni di diagnostica**.
1. Abilitare le matrici di base aggiornando l' **account di archiviazione**.
1. Selezionare **Salva**.

   ![Abilitazione del monitoraggio a livello di guest](./media/vm-certification-issues-solutions-1.png)

Per verificare che le estensioni della macchina virtuale siano attivate correttamente, eseguire le operazioni seguenti:

1. Nella macchina virtuale selezionare la scheda **estensioni VM** e quindi verificare lo stato dell' **estensione di diagnostica per Linux**.
    * Se lo stato è *provisioning riuscito*, il test case estensioni è stato superato.  
    * Se lo stato è *provisioning non riuscito*, le estensioni test case non sono riuscite ed è necessario impostare il flag finalizzato.

      ![Screenshot che mostra l'esito positivo del provisioning](./media/vm-certification-issues-solutions-2.png)

      Se l'estensione della macchina virtuale ha esito negativo, vedere [usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md) per abilitarla. Se non si vuole abilitare l'estensione della macchina virtuale, contattare il team di supporto e chiedere di disabilitarla.

## <a name="vm-provisioning-issue"></a>Problema di provisioning della macchina virtuale

Assicurarsi di aver seguito rigorosamente il processo di provisioning della macchina virtuale prima di inviare l'offerta. Per visualizzare il formato JSON per il provisioning della VM, vedere la pagina relativa alla [certificazione delle immagini di macchine virtuali di Azure](azure-vm-image-certification.md).

I problemi di provisioning possono includere gli scenari di errore seguenti:

|Scenario|Errore|Motivo|Soluzione|
|---|---|---|---|
|1|Disco rigido virtuale (VHD) non valido|Se il valore del cookie specificato nel piè di pagina del disco rigido virtuale non è corretto, il disco rigido virtuale verrà considerato non valido.|Ricreare l'immagine e inviare la richiesta.|
|2|Tipo di BLOB non valido|Il provisioning della macchina virtuale non è riuscito perché il blocco usato è un tipo di BLOB anziché un tipo di pagina.|Ricreare l'immagine e inviare la richiesta.|
|3|Timeout del provisioning o generalizzato in modo non corretto|Si è verificato un problema con la generalizzazione delle macchine virtuali.|Ricreare l'immagine con generalizzazione e inviare la richiesta.|

> [!NOTE]
> Per ulteriori informazioni sulla generalizzazione delle macchine virtuali, vedere:
> - [Documentazione di Linux](create-azure-vm-technical-asset.md#generalize-the-image)
> - [Documentazione di Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

## <a name="software-compliance-for-windows"></a>Conformità software per Windows

Se la richiesta di immagine di Windows viene rifiutata a causa di un problema di conformità del software, è possibile che sia stata creata un'immagine di Windows con l'istanza di SQL Server installata anziché acquisire l'immagine di base della versione di SQL corrispondente da Azure Marketplace.

Non creare un'immagine di Windows personalizzata con SQL Server installato. Usare invece le immagini di base SQL approvate (Enterprise/standard/Web) da Azure Marketplace.

Se si sta tentando di installare Visual Studio o un prodotto concesso in licenza con Office, contattare il team di supporto per l'approvazione precedente.

Per altre informazioni sulla selezione di una base approvata, vedere [creare le risorse tecniche della macchina virtuale di Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base).

## <a name="tool-kit-test-case-execution-failed"></a>Esecuzione test case del kit di strumenti non riuscita 

Microsoft Certification Toolkit può essere utile per eseguire i test case e verificare che il disco rigido virtuale o l'immagine siano compatibili con l'ambiente Azure.

Scaricare [Microsoft Certification Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Test case di Linux

La tabella seguente elenca i test case di Linux che verranno eseguiti dal Toolkit. La convalida dei test è indicata nella descrizione.

|Scenario|Test case|Descrizione|
|---|---|---|
|1|Cronologia bash|I file di cronologia bash devono essere cancellati prima di creare l'immagine di macchina virtuale.|
|2|Versione dell'agente Linux|È necessario installare l'agente Linux di Azure 2.2.41 o versione successiva.|
|3|Parametri del kernel richiesti|Verifica che siano impostati i seguenti parametri del kernel: <br>Console = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Scambia partizione nel disco del sistema operativo|Verifica che le partizioni di scambio non vengano create nel disco del sistema operativo.|
|5|Partizione radice nel disco del sistema operativo|Creare una singola partizione radice per il disco del sistema operativo.|
|6|Versione OpenSSL|La versione di OpenSSL dovrebbe essere v 0.9.8 o successiva.|
|7|Versione Python|È consigliabile usare Python versione 2,6 o successiva.|
|8|Intervallo di client attivo|Impostare ClientAliveInterval su 180. Per quanto necessario, l'applicazione può essere impostata da 30 a 235. Se si Abilita SSH per gli utenti finali, questo valore deve essere impostato come illustrato.|
|9|Architettura del sistema operativo|Sono supportati solo i sistemi operativi a 64 bit.|
|10|Aggiornamento automatico|Indica se l'aggiornamento automatico dell'agente Linux è abilitato.|

### <a name="common-errors-found-while-executing-previous-test-cases"></a>Errori comuni rilevati durante l'esecuzione di test case precedenti

Nella tabella seguente sono elencati gli errori comuni rilevati durante l'esecuzione di test case precedenti:
 
|Scenario|Test case|Errore|Soluzione|
|---|---|---|---|
|1|Versione dell'agente Linux test case|La versione minima dell'agente Linux è 2.2.41 o successiva. Questo requisito è stato obbligatorio a partire dal 1 ° maggio 2020.|Aggiornare la versione dell'agente Linux e deve essere 2,241 o successiva. Per altre informazioni, vedere la [pagina relativa all'aggiornamento della versione dell'agente Linux](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).|
|2|Cronologia bash test case|Verrà visualizzato un errore se la dimensione della cronologia bash nell'immagine inviata è superiore a 1 kilobyte (KB). La dimensione è limitata a 1 KB per garantire che tutte le informazioni potenzialmente riservate non vengano acquisite nel file di cronologia bash.|Per risolvere il problema, montare il disco rigido virtuale in qualsiasi altra VM funzionante e apportare le modifiche desiderate (ad esempio, eliminare i file di cronologia *. bash* ) per ridurre le dimensioni a un numero minore o uguale a 1 KB.|
|3|Parametro kernel obbligatorio test case|Questo errore viene visualizzato quando il valore per la **console** non è impostato su **ttyS0**. Verificare eseguendo il comando seguente:<br>`cat /proc/cmdline`|Impostare il valore per **console** su **ttyS0**e inviare nuovamente la richiesta.|
|4|test case intervallo ClientAlive|Se il risultato del Toolkit restituisce un risultato non riuscito per questo test case, esiste un valore non appropriato per **ClientAliveInterval**.|Impostare il valore di **ClientAliveInterval** su un valore minore o uguale a 235, quindi inviare nuovamente la richiesta.|

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

Se vengono rilevati errori con i test case precedenti, fare riferimento alla colonna **Descrizione** nella tabella per la soluzione. Se sono necessarie altre informazioni, contattare il team di supporto. 

## <a name="data-disk-size-verification"></a>Verifica delle dimensioni del disco dati

Se la dimensione di qualsiasi richiesta inviata con il disco dati è maggiore di 1023 gigabyte (GB), la richiesta non verrà approvata. Questa regola si applica sia a Linux che a Windows.

Inviare nuovamente la richiesta con una dimensione minore o uguale a 1023 GB.

## <a name="os-disk-size-validation"></a>Convalida delle dimensioni del disco del sistema operativo

Per le limitazioni sulle dimensioni del disco del sistema operativo, fare riferimento alle regole seguenti. Quando si invia una richiesta, verificare che le dimensioni del disco del sistema operativo siano comprese nel limite per Linux o Windows.

|OS|Dimensioni del disco rigido virtuale consigliate|
|---|---|
|Linux|da 30 GB a 1023 GB|
|Windows|da 30 GB a 250 GB|

Poiché le macchine virtuali consentono l'accesso al sistema operativo sottostante, verificare che le dimensioni del disco rigido virtuale siano sufficientemente grandi per il disco rigido virtuale. Poiché i dischi non sono espandibili senza tempi di inattività, usare una dimensione del disco da 30 GB a 50 GB.

|Dimensioni VHD|Dimensioni effettive occupate|Soluzione|
|---|---|---|
|>500 TB (TiB)|n/d|Contattare il team di supporto per l'approvazione di un'eccezione.|
|250-500 TiB|Differenza >200 gibibyte (GiB) dalle dimensioni del BLOB|Contattare il team di supporto per l'approvazione di un'eccezione.|

> [!NOTE]
> Dimensioni maggiori del disco comportano costi maggiori e comporteranno un ritardo durante il processo di installazione e di replica. A causa di questo ritardo e costo, il team di supporto potrebbe cercare di giustificare l'approvazione dell'eccezione.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test di verifica patch WannaCry per Windows

Per evitare un potenziale attacco correlato al virus WannaCry, verificare che tutte le richieste di immagini Windows vengano aggiornate con la patch più recente.

Per controllare la versione di Windows Server con patch per i dettagli del sistema operativo e la versione minima supportata, vedere la tabella seguente: 

È possibile verificare la versione del file di immagine da `C:\windows\system32\drivers\srv.sys` o `srv2.sys` .

> [!NOTE]
> Windows Server 2019 non presenta requisiti di versione obbligatori.

|OS|Versione|
|---|---|
|Windows serve 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|R2 per Windows Server 2012|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|N/D|

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
||7.5|3.10.0-862.34.2|
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

Seguire le linee guida per la conversione da un disco non elaborato a un disco rigido virtuale e assicurarsi che le dimensioni del disco non elaborato siano un multiplo di 1 MB. Per ulteriori informazioni, vedere [informazioni per le distribuzioni non approvate](../../virtual-machines/linux/create-upload-generic.md).

## <a name="vm-access-denied"></a>Accesso alla VM negato

Se si verificano problemi di accesso negato durante l'esecuzione dei test case nella macchina virtuale, è possibile che si verifichino privilegi insufficienti per eseguire i test case.

Verificare se è abilitato l'accesso corretto per l'account in cui sono in esecuzione i test case autonomi. Se l'accesso non è abilitato, abilitarlo per l'esecuzione dei test case. Se non si vuole abilitare l'accesso, è possibile condividere i risultati della test case automatica con il team di supporto.

## <a name="download-failure"></a>Errore di download
    
Vedere la tabella seguente per eventuali problemi che si verificano quando si scarica l'immagine di macchina virtuale usando un URL di firma di accesso condiviso (SAS).

|Scenario|Errore|Motivo|Soluzione|
|---|---|---|---|
|1|BLOB non trovato|Il disco rigido virtuale può essere eliminato o spostato dalla posizione specificata.|| 
|2|BLOB in uso|Il disco rigido virtuale viene usato da un altro processo interno.|Il disco rigido virtuale deve trovarsi nello stato usato quando lo si Scarica usando un URL di firma di accesso condiviso.|
|3|URL SAS non valido|L'URL SAS associato per il disco rigido virtuale non è corretto.|Ottenere l'URL SAS corretto.|
|4|Firma non valida|L'URL SAS associato per il disco rigido virtuale non è corretto.|Ottenere l'URL SAS corretto.|
|6|Intestazione condizionale HTTP|L'URL SAS non è valido.|Ottenere l'URL SAS corretto.|
|7|Nome VHD non valido|Controllare se sono presenti caratteri speciali, ad esempio un segno di percentuale (%) o virgolette ("), esiste nel nome del disco rigido virtuale.|Rinominare il file VHD rimuovendo i caratteri speciali.|

## <a name="first-mb-2048-kb-partition-only-for-linux"></a>Prima partizione MB (2048 KB) (solo per Linux)

Quando si invia il disco rigido virtuale, assicurarsi che i primi 2048 KB del disco rigido virtuale siano vuoti. In caso contrario, la richiesta avrà esito negativo *.

>[!NOTE]
>* Per alcune immagini speciali, ad esempio quelle basate sulle immagini di base di Windows di Azure tratte da Azure Marketplace, viene verificata la presenza di un tag di fatturazione e viene ignorata la partizione MB se il tag di fatturazione è presente e corrisponde ai valori interni disponibili.

## <a name="default-credentials"></a>Credenziali predefinite

Assicurarsi sempre che le credenziali predefinite non vengano inviate con il VHD inviato. L'aggiunta di credenziali predefinite rende il disco rigido virtuale più vulnerabile alle minacce per la sicurezza. Al contrario, creare credenziali personalizzate quando si invia il disco rigido virtuale.
  
## <a name="datadisk-mapped-incorrectly"></a>Mapping di DataDisk non corretto

Quando una richiesta viene inviata con più dischi dati, ma l'ordine non è in sequenza, questo viene considerato un problema di mapping. Se ad esempio sono presenti tre dischi dati, l'ordine di numerazione deve essere *0, 1, 2*. Qualsiasi altro ordine viene considerato un problema di mapping.

Inviare nuovamente la richiesta con la sequenziazione corretta dei dischi dati.

## <a name="incorrect-os-mapping"></a>Mapping del sistema operativo errato

Quando viene creata un'immagine, è possibile che venga eseguito il mapping a o che l'etichetta del sistema operativo non sia corretta. Ad esempio, quando si seleziona **Windows** come parte del nome del sistema operativo durante la creazione dell'immagine, il disco del sistema operativo deve essere installato solo con Windows. Lo stesso requisito si applica a Linux.

## <a name="vm-not-generalized"></a>VM non generalizzata

Se tutte le immagini tratte da Azure Marketplace devono essere riutilizzate, il disco rigido virtuale del sistema operativo deve essere generalizzato.

* Per **Linux**, il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata.

  Nella finestra SSH immettere il comando seguente: `sudo waagent -deprovision+user`

* Per **Windows**, le immagini di Windows vengono generalizzate mediante `sysreptool` .

Per ulteriori informazioni su questo strumento, vedere [Cenni preliminari sulla preparazione del sistema (Sysprep)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

## <a name="datadisk-errors"></a>Errori DataDisk

Per le soluzioni a errori correlati al disco dati, usare la tabella seguente:

|Errore|Motivo|Soluzione|
|---|---|---|
|`DataDisk- InvalidUrl:`|Questo errore può verificarsi a causa di un numero non valido specificato per il numero di unità logica (LUN) quando viene inviata l'offerta.|Verificare che la sequenza di numeri LUN per il disco dati si trovi nel centro per i partner.|
|`DataDisk- NotFound:`|Questo errore può verificarsi a causa di un disco dati che non si trova in corrispondenza di un URL SAS specificato.|Verificare che il disco dati si trovi nell'URL SAS specificato nella richiesta.|

## <a name="remote-access-issue"></a>Problema di accesso remoto

Se l'opzione Remote Desktop Protocol (RDP) non è abilitata per l'immagine di Windows, verrà visualizzato questo errore. 

Abilitare l'accesso RDP per le immagini Windows prima di inviarli.

## <a name="bash-history-failed"></a>Cronologia bash non riuscita

Questo errore viene visualizzato se la dimensione della cronologia bash nell'immagine inviata è superiore a 1 kilobyte (KB). La dimensione è limitata a 1 KB per garantire che tutte le informazioni potenzialmente riservate non vengano acquisite nel file di cronologia bash.

Di seguito sono riportati i passaggi per eliminare la "cronologia bash".

Passaggio 1. Distribuire la macchina virtuale e fare clic sull'opzione "Esegui comando" su portale di Azure.
![Esegui comando su portale di Azure](./media/vm-certification-issues-solutions-3.png)

Passaggio 2: Selezionare la prima opzione "RunShellScript" ed eseguire il comando seguente.

Comando: "cat/dev/null > ~/.bash_history && History-c" ![ bash History Command on portale di Azure](./media/vm-certification-issues-solutions-4.png)

Passaggio 3. Al termine dell'esecuzione del comando, riavviare la macchina virtuale.

Passaggio 4. Generalizzare la VM, estrarre il disco rigido virtuale dell'immagine e arrestare la macchina virtuale.

Passaggio 5.     Re-Submit l'immagine generalizzata.

## <a name="requesting-exceptions-custom-templates-on-vm-images-for-selective-tests"></a>Richiesta di eccezioni (modelli personalizzati) sulle immagini di VM per i test selettivi

Gli editori possono contattare per richiedere eccezioni per pochi test eseguiti durante la certificazione della macchina virtuale. Le eccezioni vengono fornite in casi estremamente rari quando Publisher fornisce l'evidenza per supportare la richiesta.
Il team di certificazione si riserva il diritto di negare o approvare le eccezioni in qualsiasi momento.

Nelle sezioni seguenti verranno illustrati gli scenari principali in cui vengono richieste le eccezioni e come richiedere un'eccezione.

Scenari per l'eccezione

Esistono tre scenari/casi in cui i Publisher richiedono in genere queste eccezioni. 

* **Eccezione per uno o più test case:** Gli editori possono raggiungere le eccezioni di richiesta del supporto per l' [editore del Marketplace](https://aka.ms/marketplacepublishersupport) per i test case. 

* **Macchine virtuali bloccate/nessun accesso alla radice:** Pochi autori hanno scenari in cui le macchine virtuali devono essere bloccate in quanto hanno un software, ad esempio i firewall installati nella macchina virtuale. 
       In questo caso, i server di pubblicazione possono scaricare lo [strumento di test certificato](https://aka.ms/AzureCertificationTestTool) e fornire il report al supporto per gli editori del [Marketplace](https://aka.ms/marketplacepublishersupport)


* **Modelli personalizzati:** Alcuni editori pubblicano immagini di VM che richiedono un modello ARM personalizzato per distribuire le macchine virtuali. In questo caso, è necessario che gli editori forniscano i modelli personalizzati del supporto per l' [editore del Marketplace](https://aka.ms/marketplacepublishersupport) , in modo che possano essere usati dal team di certificazione per la convalida. 

### <a name="information-to-provide-for-exception-scenarios"></a>Informazioni da fornire per gli scenari di eccezione

Gli editori devono rivolgersi al supporto tecnico di [Marketplace Publisher](https://aka.ms/marketplacepublishersupport) per la richiesta di eccezioni per lo scenario precedente con le informazioni aggiuntive seguenti:

   1.   ID editore: ID editore nel portale del centro per i partner
   2.   ID offerta/nome: ID offerta/nome per cui è richiesta l'eccezione 
   3.   ID dello SKU/piano: ID del piano/SKU dell'offerta di macchina virtuale per cui è richiesta l'eccezione
   4.    Version (versione): la versione dell'offerta di macchina virtuale per cui è richiesta l'eccezione
   5.   Tipo di eccezione: test, macchine virtuali bloccate, modelli personalizzati
   6.   Motivo della richiesta: motivo dell'eccezione e informazioni sui test da esentare 
   7. Sequenza temporale-data fino alla quale è stata richiesta questa eccezione 
   8.   Allegato: collegare eventuali documenti di evidenza di importanza. Per le VM bloccate, collegare il report di test e per i modelli personalizzati, fornire il modello ARM personalizzato come allegato. La mancata connessione del report per le VM bloccate e il modello ARM personalizzato per i modelli personalizzati determinerà un attacco Denial of request


## <a name="next-steps"></a>Passaggi successivi

In caso di domande o commenti e suggerimenti, contattare il [supporto tecnico del centro](https://partner.microsoft.com/support/v2/?stage=1)per i partner.
