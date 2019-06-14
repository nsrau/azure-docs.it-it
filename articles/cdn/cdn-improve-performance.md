---
title: Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure | Documentazione Microsoft
description: Informazioni su come migliorare la velocità di trasferimento dei file e aumentare le prestazioni di caricamento delle pagine comprimendo i file nella rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: afe959e80b339db5112fa97fd79d0528390e3954
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60637006"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure
La compressione dei file è un metodo semplice ed efficace per aumentare la velocità di trasferimento dei file e migliorare le prestazioni di caricamento delle pagine mediante la riduzione delle dimensioni del file prima che venga inviato dal server. Riduce i costi della larghezza di banda e offre un'esperienza più reattiva per gli utenti.

Esistono due modi per abilitare la compressione dei file:

- Abilitare la compressione nel server di origine. In questo caso, la rete CDN di Azure trasmette i file compressi e li distribuisce ai client che li richiedono.
- Abilitare la compressione direttamente nei server POP della rete CDN (*compressione in tempo reale*). In questo caso, la rete CDN comprime i file e li trasmette agli utenti finali anche se non erano stati compressi dal server di origine.

> [!IMPORTANT]
> La propagazione delle modifiche alla configurazione della rete CDN di Azure può richiedere tempo: 
> - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
> - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
> - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti. 
> 
> Se si configura la compressione per l'endpoint della rete CDN per la prima volta, prima di procedere alla risoluzione dei problemi provare ad attendere 1-2 ore per assicurarsi che le impostazioni di compressione siano state propagate ai POP.

## <a name="enabling-compression"></a>Abilitare la compressione
I livelli della rete CDN Standard e Premium forniscono la stessa funzionalità di compressione, ma l'interfaccia utente è diversa. Per altre informazioni sulle differenze tra i livelli della rete CDN Standard e Premium, vedere [Panoramica della rete CDN di Azure](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Profili di rete CDN standard 
> [!NOTE]
> Questa sezione si applica ai profili della **rete CDN Standard di Azure con tecnologia Microsoft**, della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Standard di Azure con tecnologia Akamai**.
> 
> 

1. Nella pagina del profilo della rete CDN selezionare l'endpoint della rete CDN che si vuole gestire.

    ![Endpoint del profilo di rete CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Viene aperta la pagina dell'endpoint della rete CDN.
2. Selezionare **Compressione**.

    ![Selezione della compressione della rete CDN](./media/cdn-file-compression/cdn-compress-select-std.png)

    Viene visualizzata la pagina della compressione.
3. Selezionare **On** per attivare la compressione.

    ![Opzioni di compressione dei file della rete CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Usare i tipi MIME predefiniti oppure modificare l'elenco aggiungendo o rimuovendo tipi MIME.

   > [!TIP]
   > Anche se è possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4 o JPG.
   > 

   > [!NOTE]
   > La modifica dell'elenco predefinito di tipi MIME non è attualmente supportata nella Rete CDN di Azure Standard di Microsoft.
   > 

5. Dopo aver apportato le modifiche, selezionare **Salva**.

### <a name="premium-cdn-profiles"></a>Profili di rete CDN Premium
> [!NOTE]
> Questa sezione si applica solo ai profili della **rete CDN Premium di Azure fornita da Verizon**.
> 

1. Nella pagina del profilo della rete CDN selezionare **Gestisci**.

    ![Selezione di Gestisci per la rete CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **HTTP Large** (HTTP esteso) e quindi sul riquadro a comparsa **Impostazioni cache**. Selezionare **Compressione**.

    ![Selezione della compressione della rete CDN](./media/cdn-file-compression/cdn-compress-select.png)

    Vengono visualizzate le opzioni di compressione.

    ![Opzioni di compressione dei file della rete CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Abilitare la compressione selezionando **Compressione abilitata**. Nella casella **Tipi di file** immettere i tipi MIME da comprimere sotto forma di elenco delimitato da virgole, senza spazi.

   > [!TIP]
   > Anche se è possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4 o JPG.
   > 

4. Dopo aver apportato le modifiche, selezionare **Aggiorna**.

## <a name="compression-rules"></a>Regole di compressione

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Profili di rete CDN Standard di Azure con tecnologia Microsoft

Per i profili della **rete CDN Standard di Azure con tecnologia Microsoft** vengono compressi solo i file idonei. Per essere idoneo per la compressione, un file deve essere:
- Essere un tipo MIME che è rimasto [configurato per la compressione](#enabling-compression).
- Essere superiori a 1 KB
- Essere inferiori a 8 MB

Questi profili supportano le codifiche di compressione seguenti:
- gzip (GNU Zip)
- brotli 

Se la richiesta supporta più di un tipo di compressione, la compressione brotli ha la precedenza.

Quando una richiesta per una risorsa indica la compressione gzip e i risultati della richiesta non sono presenti nella cache, la rete CDN di Azure esegue la compressione gzip della risorsa direttamente nel server POP. In seguito, il file compresso viene gestito nella cache.

### <a name="azure-cdn-from-verizon-profiles"></a>Profili di rete CDN di Azure con tecnologia Verizon

Per i profili di **rete CDN Standard di Azure con tecnologia Verizon** e di **rete CDN Premium di Azure con tecnologia Verizon**, vengono compressi solo i file idonei. Per essere idoneo per la compressione, un file deve essere:
- Maggiore di 128 byte
- Essere inferiore a 3 MB

Questi profili supportano le codifiche di compressione seguenti:
- gzip (GNU Zip)
- DEFLATE
- bzip2
- brotli 

Se la richiesta supporta più di un tipo di compressione, questi tipi di compressione hanno la precedenza sulla compressione brotli.

Se in una richiesta per una risorsa viene specificata la compressione brotli (intestazione HTTP `Accept-Encoding: br`) e mancano i risultati della richiesta in una cache, la rete CDN di Azure esegue la compressione brotli della risorsa nel server POP di origine. In seguito, il file compresso viene gestito nella cache.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Profili di rete CDN Standard di Azure con tecnologia Akamai

Per i profili di **rete CDN Standard di Azure con tecnologia Akamai**, tutti i file sono idonei per la compressione. Il file, tuttavia, deve essere un tipo MIME [configurato per la compressione](#enabling-compression).

Questi profili supportano solo codifiche di compressione gzip. Quando un endpoint del profilo richiede un file con codifica gzip, i file vengono sempre richiesti dall'origine indipendentemente dalla richiesta del client. 

## <a name="compression-behavior-tables"></a>Tabelle di comportamento della compressione
Le tabelle seguenti descrivono il comportamento della compressione della rete CDN di Azure per ogni scenario:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Compressione disabilitata o file non idoneo per la compressione
| Formato richiesto del client tramite l'intestazione Accept-Encoding | Formato del file memorizzato nella cache non valido | Risposta della rete CDN al client | Note&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Compresso |Compresso |Compresso | |
| Compresso |Non compresso |Non compresso | |
| Compresso |Non memorizzato nella cache |Compressa o non compressa |La rete CDN esegue la compressione in base alla risposta di origine. |
| Non compresso |Compresso |Non compresso | |
| Non compresso |Non compresso |Non compresso | |
| Non compresso |Non memorizzato nella cache |Non compresso | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Compressione abilitata e file idoneo per la compressione
| Formato richiesto del client tramite l'intestazione Accept-Encoding | Formato del file memorizzato nella cache non valido | Risposta della rete CDN al client | Note |
| --- | --- | --- | --- |
| Compresso |Compresso |Compresso |Transcodifica della rete CDN tra formati supportati |
| Compresso |Non compresso |Compresso |Compressione da parte della rete CDN |
| Compresso |Non memorizzato nella cache |Compresso |La rete CDN esegue la compressione se l'origine restituisce un file non compresso. <br/>**La rete CDN di Azure fornita da Verizon** effettua il passaggio del file non compresso per la prima richiesta, per poi eseguire la compressione e la memorizzazione nella cache del file per le richieste successive. <br/>I file con l'intestazione `Cache-Control: no-cache` non vengono mai compressi. |
| Non compresso |Compresso |Non compresso |Decompressione da parte della rete CDN. |
| Non compresso |Non compresso |Non compresso | |
| Non compresso |Non memorizzato nella cache |Non compresso | |

## <a name="media-services-cdn-compression"></a>Compressione della rete CDN dei servizi multimediali
Per gli endpoint abilitati per lo streaming della rete CDN di Servizi multimediali, la compressione è abilitata per impostazione predefinita per i tipi MIME seguenti: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Vedere anche
* [Risoluzione dei problemi della compressione dei file CDN](cdn-troubleshoot-compression.md)    

