---
title: Certificazione di macchine virtuali - Problemi e soluzioni
description: Questo articolo illustra i messaggi di errore comuni per le immagini di macchina virtuale. Vengono inoltre illustrate le soluzioni correlate
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: 7bd3f1a5b242ee5196e92456cb3fc8c97f8f5b27
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958532"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>Certificazione di macchine virtuali - Problemi e soluzioni

Quando si pubblicano le immagini della macchina virtuale in Azure Marketplace, il team di Azure convalida l'immagine della VM per garantirne l'avvio, la sicurezza e la compatibilità con Azure. Se uno dei test di qualità elevata ha esito negativo, la pubblicazione avrà esito negativo con un messaggio contenente l'errore.

Questo articolo illustra i messaggi di errore comuni per le immagini di macchina virtuale. Vengono inoltre illustrate le soluzioni correlate:

> [!NOTE]
> In caso di domande o commenti e suggerimenti, contattare il [supporto tecnico del centro](https://partner.microsoft.com/support/v2/?stage=1)per i partner.

## <a name="approved-base-image"></a>Immagine di base approvata

Quando si invia una richiesta di ripubblicazione dell'immagine con gli aggiornamenti, il test case di verifica del numero di parte potrebbe non riuscire. In tale istanza, l'immagine non verrà approvata.

Questo errore si verifica quando si usa un'immagine di base che appartiene a un altro server di pubblicazione ed è stata aggiornata l'immagine. In questa situazione non sarà consentito pubblicare l'immagine.

Per risolvere questo problema, recuperare l'immagine più recente da Azure Marketplace e apportare le modifiche all'immagine. Per visualizzare le immagini di base approvate in cui è possibile cercare l'immagine, vedere gli argomenti seguenti:

- [Linux-immagini](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-immagini](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base))

## <a name="vm-extension-failure"></a>Errore di estensione della macchina virtuale

Per verificare se l'immagine supporta o meno l'estensione della macchina virtuale, seguire questa procedura:

Abilitare le estensioni della macchina virtuale:

1. Selezionare la VM Linux.
2. Passare a **impostazioni di diagnostica**.
3. Abilitare le matrici di base aggiornando l' **account di archiviazione**.
4. Selezionare **Salva**.

   ![Abilitazione del monitoraggio a livello di guest](./media/vm-certification-issues-solutions-1.png)

Verificare che le estensioni della macchina virtuale siano attivate correttamente:

5. Passare alla scheda **estensioni VM** della macchina virtuale e verificare l' **estensione di diagnostica per Linux**.
6. Se lo stato è il **provisioning riuscito** , il test case estensioni è stato superato.
7. Se lo stato è **provisioning non riuscito** , le estensioni test case non sono riuscite ed è necessario impostare il flag finalizzato.

   ![Provisioning riuscito](./media/vm-certification-issues-solutions-2.png)

   Se l'estensione della macchina virtuale ha esito negativo, passare a [usare l'estensione di diagnostica Linux per monitorare le metriche e i log](../../virtual-machines/extensions/diagnostics-linux.md) per abilitarla. Se non si vuole abilitare l'estensione della macchina virtuale, contattare il team di supporto e chiedere di disabilitare l'estensione.

## <a name="virtual-machine-provisioning-issue"></a>Problema di provisioning della macchina virtuale

Prima di inviare l'offerta, controllare che il processo di provisioning sia rigorosamente seguito per la macchina virtuale. Per visualizzare il formato JSON per il provisioning della macchina virtuale, passare alla [certificazione delle immagini della macchina virtuale (VM) di Azure](azure-vm-image-certification.md).

I problemi di provisioning possono includere gli scenari di errore seguenti:

|S.NO|error|reason|Soluzione|
|---|---|---|---|
|1|Disco rigido virtuale (VHD) non valido|Se il valore del cookie specificato nel piè di pagina del disco rigido virtuale non è corretto, il disco rigido virtuale verrà considerato non valido.|Ricreare l'immagine e inviare la richiesta.|
|2|Tipo di BLOB non valido|Il provisioning della macchina virtuale non è riuscito perché il blocco usato è un tipo di BLOB anziché un tipo di pagina.|Ricreare l'immagine e inviare la richiesta.|
|3|Timeout del provisioning o generalizzato in modo non corretto|Si è verificato un problema con la generalizzazione delle macchine virtuali.|Ricreare l'immagine con generalizzazione e inviare la richiesta.|

> [!NOTE]
> Per la documentazione relativa alla generalizzazione delle macchine virtuali, seguire i collegamenti seguenti:
> - [Linux](create-azure-vm-technical-asset.md#generalize-the-image))
> - [Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep))

## <a name="software-compliance-for-windows"></a>Conformità software per Windows

Se la richiesta di immagine di Windows viene rifiutata a causa della conformità del software, è possibile che sia stata creata un'immagine di Windows con il server SQL installato, anziché acquisire l'immagine di base di SQL versione pertinente da Azure Marketplace.

Non creare un'immagine di Windows personalizzata con SQL Server installato. Usare invece le immagini di base SQL approvate (Enterprise/standard/Web) da Azure Marketplace.

Se si sta tentando di installare Visual Studio o un prodotto concesso in licenza di Office, contattare il team di supporto per l'approvazione precedente.

Per altre informazioni, vedere [creare le risorse tecniche della macchina virtuale di Azure](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)) per selezionare una base approvata.

## <a name="tool-kit-test-case-execution-failed"></a>Esecuzione test case del kit di strumenti non riuscita

Microsoft Certification Toolkit consentirà di eseguire i test case per verificare che il disco rigido virtuale/immagine sia compatibile con l'ambiente Azure.

Scaricare [Microsoft Certification Toolkit](azure-vm-image-certification.md).

## <a name="linux-test-cases"></a>Test case di Linux

Di seguito sono riportati i test case di Linux che verranno eseguiti dal Toolkit. La convalida dei test è indicata nella descrizione.

|Numero S.|test case|description|
|---|---|---|
|1|Cronologia bash|I file di cronologia bash devono essere cancellati prima di creare l'immagine di macchina virtuale.|
|2|Versione dell'agente Linux|È necessario installare l'agente Linux di Azure 2.2.41 e versioni successive.|
|3|Parametri del kernel richiesti|Verifica che siano impostati i seguenti parametri del kernel: <br>Console = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|Scambia partizione nel disco del sistema operativo|Verifica che le partizioni di scambio non vengano create nel disco del sistema operativo.|
|5|Partizione radice nel disco del sistema operativo|Creare una singola partizione radice per il disco del sistema operativo.|
|6|Versione OpenSSL|La versione di OpenSSL deve essere maggiore o uguale a v 0.9.8.|
|7|Versione di Python|È consigliabile usare Python versione 2.6 +.|
|8|Intervallo di client attivo|Impostare ClientAliveInterval su 180. Per le esigenze dell'applicazione, è possibile impostare un intervallo compreso tra 30 e 235. Se si Abilita SSH per gli utenti finali, questo valore deve essere impostato come illustrato.|
|9|Architettura del sistema operativo|Sono supportati solo i sistemi operativi a 64 bit.|
|10|Aggiornamento automatico|Indica se l'aggiornamento automatico dell'agente Linux è abilitato.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>Errori comuni rilevati durante l'esecuzione dei test case precedenti

Errori comuni rilevati durante l'esecuzione dei test case precedenti.
 
|S.NO|test case|error|Soluzione|
|---|---|---|---|
|1|Versione dell'agente Linux test case|La versione minima dell'agente Linux è 2,241 o successiva. Questo requisito è stato obbligatorio a partire dal 1 ° maggio 2020|Per [inviare la richiesta](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support), è necessario aggiornare l'immagine con la versione richiesta.|
|2|Test case cronologia bash|Verrà visualizzato un errore se la dimensione della cronologia bash nell'immagine inviata è superiore a 1 KB. La dimensione è limitata a 1 KB per garantire che tutte le informazioni potenzialmente riservate non vengano acquisite nel file di cronologia bash.|Per risolvere il problema, montare il disco rigido virtuale in qualsiasi altra VM funzionante e apportare le modifiche (ad esempio, eliminare i `.bash` file di cronologia) per ridurre le dimensioni a un numero minore o uguale a 1 KB.|
|3|Test case del parametro kernel obbligatorio|Questo errore viene visualizzato quando il valore per la **console** non è impostato su **ttyS0**. Verificare eseguendo il comando:<br>`cat /proc/cmdline`|Impostare il valore per **console** su **ttyS0** e inviare nuovamente la richiesta.|
|4|Test case intervallo ClientAlive|Se il risultato del Toolkit restituisce un risultato non riuscito per questo test case, esiste un valore non appropriato per **ClientAliveInterval**.|Impostare il valore di **ClientAliveInterval** su un valore minore o uguale a 235, quindi inviare nuovamente la richiesta.|

### <a name="windows-test-cases"></a>Test case di Windows

Di seguito sono riportati i test case di Windows che verranno eseguiti dal Toolkit. La convalida dei test è indicata nella descrizione.

|Numero S.|test case|description|
|---|---|---|---|
|1|Architettura del sistema operativo|Azure supporta solo i sistemi operativi a 64 bit.|
|2|Dipendenza account utente|L'esecuzione dell'applicazione non deve dipendere dall'account Administrator.|
|3|Cluster di failover|La funzionalità clustering di failover di Windows Server non è ancora supportata. l'applicazione non deve dipendere da questa funzionalità.|
|4|IPV6|IPv6 non è ancora supportato nell'ambiente Azure. L'applicazione non deve dipendere da questa funzionalità.|
|5|DHCP|Il ruolo del server Dynamic Host Configuration Protocol non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|6|Hyper-V|Il ruolo del server Hyper-V non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|7|Accesso remoto|Il ruolo del server accesso remoto (accesso diretto) non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|8|Rights Management Services|Servizi Rights Management. Il ruolo del server non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|9|Servizi di distribuzione Windows|Servizi di distribuzione Windows. Il ruolo del server non è ancora supportato. L'applicazione non deve dipendere da questa funzionalità.|
|10|Crittografia unità BitLocker|Crittografia unità BitLocker non è supportato nel disco rigido del sistema operativo, ma può essere usato nei dischi dati.|
|11|Internet Storage Name Server|La funzionalità server del nome di archiviazione Internet non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|12|Multipath I/O|Multipath I/O. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|13|Bilanciamento carico di rete|Bilanciamento carico di rete. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|14|Protocollo PNRP (Peer Name Resolution Protocol)|Protocollo di risoluzione del nome peer. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|15|Servizi SNMP|La funzionalità servizi SNMP non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|16|Windows Internet Name Service|Windows Internet Name Service. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|
|17|Servizio LAN Wireless|Servizio LAN wireless. Questa funzionalità server non è ancora supportata. L'applicazione non deve dipendere da questa funzionalità.|

Se vengono rilevati errori con i test case precedenti, fare riferimento alla colonna **Descrizione** nella tabella precedente per la soluzione. Se sono necessarie altre informazioni, contattare il team di supporto. 

## <a name="data-disk-size-verification"></a>Verifica delle dimensioni del disco dati

Se la dimensione di qualsiasi richiesta inviata con il disco dati è maggiore di 1023 GB, la richiesta non verrà approvata. Questa regola si applica a entrambe le finestre & Linux.

inviare nuovamente la richiesta con una dimensione minore o uguale a 1023 GB.

## <a name="os-disk-size-validation"></a>Convalida delle dimensioni del disco del sistema operativo

Per le limitazioni sulle dimensioni del disco del sistema operativo, fare riferimento alle regole seguenti. Quando si invia una richiesta, verificare che le dimensioni del disco del sistema operativo siano comprese nel limite per Linux o Windows.

|OS|dimensioni del disco rigido virtuale consigliate|
|---|---|
|Linux|da 30 GB a 1023 GB|
|Windows|da 30 GB a 250 GB|

Poiché le macchine virtuali consentono l'accesso al sistema operativo sottostante, verificare che le dimensioni del disco rigido virtuale siano sufficientemente grandi per il disco rigido virtuale. Poiché i dischi non sono espandibili senza tempi di inattività, utilizzare dimensioni del disco comprese tra 30 e 50 GB.

|Dimensioni VHD|dimensioni effettive occupate|Soluzione|
|---|---|---|
|>500 TiB|n/d|Contattare il team di supporto per l'approvazione di un'eccezione.|
|250-500 TiB|>200 GiB diverso dalla dimensione del BLOB|Contattare il team di supporto per l'approvazione di un'eccezione.|

> [!NOTE]
> Dimensioni maggiori del disco comportano costi maggiori e comporteranno un ritardo durante le fasi di provisioning e di replica. A causa di questo ritardo e costo, il team di supporto potrebbe cercare di giustificare l'approvazione dell'eccezione.

## <a name="wannacry-patch-verification-test-for-windows"></a>Test di verifica patch WannaCry per Windows

Per evitare un potenziale attacco correlato al virus WannaCry, verificare che tutte le richieste di immagini Windows vengano aggiornate con la patch più recente.

Per controllare la versione di Windows Server con patch, vedere la tabella seguente per i dettagli del sistema operativo e la versione minima supportata. 

È possibile verificare la versione del file di immagine da `C:\windows\system32\drivers\srv.sys` o `srv2.sys` .

> [!NOTE]
> WindowsServer2019 non ha requisiti di versione obbligatori.

|OS|version|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|ND|

## <a name="sack-vulnerability-patch-verification"></a>Verifica della patch della vulnerabilità di SACK

Quando si invia un'immagine Linux, è possibile che la richiesta venga rifiutata a causa di problemi di versione del kernel.

Aggiornare il kernel con una versione approvata e inviare nuovamente la richiesta. È possibile trovare la versione del kernel approvata nella tabella seguente. Il numero di versione deve essere maggiore o uguale a quello indicato di seguito.

Se l'immagine non è installata con una delle seguenti versioni del kernel, aggiornare l'immagine con le patch corrette. Per ulteriori informazioni, vedere i collegamenti seguenti. Richiedere l'approvazione necessaria al team di supporto dopo che l'immagine è stata aggiornata con le patch richieste seguenti:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|Famiglia del sistema operativo|version|kernel|
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

Tutti i dischi rigidi virtuali in Azure devono avere una dimensione virtuale allineata a un multiplo di 1 MB. Se il disco rigido virtuale non rispetta le dimensioni virtuali consigliate, è possibile che la richiesta venga rifiutata.

seguire le linee guida, quando si esegue la conversione da un disco non elaborato a un disco rigido virtuale e si deve verificare che le dimensioni del disco non elaborato siano un multiplo di 1 MB. Per altre informazioni, vedere [informazioni per le distribuzioni non approvate](../../virtual-machines/linux/create-upload-generic.md)

## <a name="vm-access-denied"></a>Accesso alla VM negato

Se si verificano problemi di accesso negato durante l'esecuzione dei test case nella macchina virtuale, è possibile che si verifichino privilegi insufficienti per eseguire i test case.

Controllare se l'accesso corretto è abilitato per l'account in cui sono in esecuzione i test case autonomi. in caso contrario, abilitare l'accesso per l'esecuzione dei test case. Se non si vuole abilitare l'accesso, è possibile condividere i risultati della test case automatica con il team di supporto.

## <a name="download-failure"></a>Errore di download
    
Vedere la tabella seguente per eventuali problemi durante il download dell'immagine di macchina virtuale con l'URL SAS.

|S.NO|error|reason|Soluzione|
|---|---|---|---|
|1|BLOB non trovato|Il disco rigido virtuale può essere eliminato o spostato dal percorso specificato|| 
|2|BLOB in uso|Il disco rigido virtuale viene usato da un altro processo interno|Il disco rigido virtuale deve trovarsi in uno stato usato durante il download usando l'URL SAS.|
|3|URL SAS non valido|L'URL SAS associato per il disco rigido virtuale non è corretto.|Ottenere l'URL SAS corretto.|
|4|Firma non valida|L'URL SAS associato per il disco rigido virtuale non è corretto.|Ottenere l'URL SAS corretto.|
|6|Intestazione condizionale HTTP|URL SAS non valido.|Ottenere l'URL SAS corretto.|
|7|Nome VHD non valido|Controllare se sono presenti caratteri speciali, ad esempio **%** o **""** , nel nome del disco rigido virtuale|Rinominare il file VHD rimuovendo i caratteri speciali|

## <a name="first-1-mb-partition"></a>Prima partizione da 1 MB

Quando si invia il disco rigido virtuale, assicurarsi che la prima partizione da 1 MB del disco rigido virtuale sia vuota. In caso contrario, la richiesta avrà esito negativo.

## <a name="default-credentials"></a>Credenziali predefinite

Assicurarsi sempre che le credenziali predefinite non vengano inviate con il VHD inviato. L'aggiunta di credenziali predefinite rende il disco rigido virtuale più vulnerabile alle minacce per la sicurezza. Al contrario, creare credenziali personalizzate quando si invia il disco rigido virtuale.
  
## <a name="datadisk-mapped-incorrectly"></a>Mapping di DataDisk non corretto

Quando una richiesta viene inviata con più dischi dati, ma l'ordine non è in sequenza, questo viene considerato un problema di mapping. Se ad esempio sono presenti tre dischi dati, l'ordine di numerazione deve essere **0, 1, 2**. Eventuali altri ordini verranno considerati come un problema di mapping.

inviare nuovamente la richiesta con la sequenziazione corretta dei dischi dati.

## <a name="incorrect-os-mapping"></a>Mapping del sistema operativo errato

Quando viene creata un'immagine, è possibile eseguirne il mapping o assegnarla all'etichetta del sistema operativo non corretta. Ad esempio, quando si seleziona **Windows** come parte del nome del sistema operativo durante la creazione dell'immagine, il disco del sistema operativo deve essere installato solo con Windows. Lo stesso vale per Linux.

## <a name="vm-not-generalized"></a>VM non generalizzata

Se tutte le immagini tratte da Azure Marketplace devono essere riutilizzate, il VHD del sistema operativo deve essere generalizzato.

Linux:

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata.

Nella finestra SSH immettere il comando seguente:`sudo waagent -deprovision+user`

Windows:

Le immagini Windows sono generalizzate con `sysreptool` .

Per ulteriori informazioni su questo strumento, vedere la [Panoramica di Sysprep (preparazione del sistema)]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)

## <a name="datadisk-error"></a>Errore DataDisk

Per le soluzioni agli errori correlati al disco dati, vedere la tabella seguente.

|Errore|reason|Soluzione|
|---|---|---|
|`DataDisk- InvalidUrl:`|Questo errore può essere dovuto a un numero non valido specificato per LUN durante l'invio dell'offerta.|Verificare che la sequenza di numeri LUN per il disco dati si trovi nel centro per i partner.|
|`DataDisk- NotFound:`|Questo errore può essere dovuto a un disco dati che non si trova in un URL SAS specificato|Verificare che il disco dati si trovi nell'URL SAS specificato nella richiesta.|

## <a name="remote-access-issue"></a>Problema di accesso remoto

Se l'opzione RDP non è abilitata per l'immagine di Windows, verrà visualizzato questo errore. 

Abilitare l'accesso RDP per le immagini Windows prima dell'invio.

## <a name="next-steps"></a>Passaggi successivi

In caso di domande o commenti e suggerimenti, contattare il [supporto tecnico del centro](https://partner.microsoft.com/support/v2/?stage=1)per i partner.
