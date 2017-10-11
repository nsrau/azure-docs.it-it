---
title: Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure | Documentazione Microsoft
description: "Informazioni su come migliorare la velocità di trasferimento di file e aumentare le prestazioni di caricamento delle pagine comprimendo i file nella rete CDN di Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 86f1f8f0aaa9a699d2f816781cef65cda868ee55
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure
La compressione è un metodo semplice ed efficace per aumentare la velocità di trasferimento dei file e migliorare le prestazioni di caricamento delle pagine mediante la riduzione delle dimensioni del file prima che venga inviato dal server. Riduce i costi della larghezza di banda e offre un'esperienza più reattiva per gli utenti.

Esistono due modi per abilitare la compressione:

* Abilitare la compressione nel server di origine. In questo caso, la rete CDN trasmette i file compressi e li distribuisce ai client che li richiedono.
* Abilitare la compressione direttamente nei server perimetrali della rete CDN. In questo caso, la rete CDN comprime i file e li trasmette agli utenti finali anche se non sono stati compressi dal server di origine.

> [!IMPORTANT]
> La propagazione attraverso la rete delle modifiche alla configurazione della rete CDN può richiedere tempo.  La propagazione dei profili della <b>Rete CDN di Azure fornita da Akamai</b> in genere dura meno di un minuto.  Per i profili della <b>rete CDN di Azure fornita da Verizon</b>, le modifiche vengono in genere applicate entro 90 minuti.  Se si configura la compressione per l'endpoint della rete CDN per la prima volta, prima di procedere alla risoluzione dei problemi provare ad attendere 1-2 ore per assicurarsi che le impostazioni di compressione siano state propagate ai POP.
> 
> 

## <a name="enabling-compression"></a>Abilitare la compressione
> [!NOTE]
> I livelli della rete CDN Standard e Premium forniscono la stessa funzionalità di compressione, ma l'interfaccia utente è diversa.  Per altre informazioni sulle differenze tra i livelli della rete CDN Standard e Premium, vedere [Panoramica della rete CDN di Azure](cdn-overview.md).
> 
> 

### <a name="standard-tier"></a>Livello Standard
> [!NOTE]
> Questa sezione si applica ai profili della **rete CDN Standard di Azure fornita da Verizon** e della **rete CDN Standard di Azure fornita da Akamai**.
> 
> 

1. Nella pagina del profilo di rete CDN fare clic sull'endpoint della rete CDN che si desidera gestire.
   
    ![Endpoint del profilo di rete CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    Viene aperta la pagina dell'endpoint della rete CDN.
2. Fare clic sul pulsante **Configura** .
   
    ![Pulsante per la gestione del profilo di rete CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    Viene aperta la pagina di configurazione della rete CDN.
3. Attivare **compressione**.
   
    ![Opzioni di compressione della rete CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Usare i tipi predefiniti, o modificare l'elenco eliminando o aggiungendo tipi di file.
   
   > [!TIP]
   > Anche se è possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4 o JPG.
   > 
 
5. Dopo aver apportato le modifiche, fare clic sul pulsante **Salva** .

### <a name="premium-tier"></a>Livello Premium
> [!NOTE]
> Questa sezione si applica ai profili di **Rete CDN Premium di Azure da Verizon** .
> 
> 

1. Nella pagina del profilo della rete CDN fare clic sul pulsante **Gestisci**.
   
    ![Pulsante per la gestione del profilo di rete CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **HTTP Large** (HTTP esteso) e quindi sul riquadro a comparsa **Impostazioni cache**.  Fare clic su **Compressione**.

    ![Selezione della compressione file](./media/cdn-file-compression/cdn-compress-select.png)
   
    Vengono visualizzate le opzioni di compressione.
   
    ![Opzioni di compressione file](./media/cdn-file-compression/cdn-compress-files.png)
3. Abilitare la compressione facendo clic sul pulsante di opzione **Compressione abilitata** .  Immettere i tipi MIM da comprimere sotto forma di elenco delimitato da virgole, senza spazi, nella casella di testo **Tipi di file** .
   
   > [!TIP]
   > Anche se è possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4 o JPG.
   > 
4. Dopo aver apportato le modifiche, fare clic sul pulsante **Aggiorna** .

## <a name="compression-rules"></a>Regole di compressione
Le tabelle seguenti descrivono il comportamento della compressione della rete CDN di Azure per ogni scenario.

> [!IMPORTANT]
> Per i profili della **rete CDN di Azure fornita da Verizon** (Standard e Premium) vengono compressi solo i file idonei.  Per essere idoneo per la compressione, un file deve essere:
> 
> * Maggiore di 128 byte.
> * Minore di 1 MB.
> 
> Questi profili supportano la codifica **gzip** (zip GNU), **deflate**, **bzip2** o **br** (Brotli). Per la codifica Brotli, la compressione viene eseguita solo sul server perimetrale. Il client/browser deve inviare la richiesta per la codifica Brotli ed è necessario che l'asset sia stato in primo luogo compresso sul lato di origine. 

> [!IMPORTANT]
> Per i profili della **rete CDN di Azure fornita da Akamai**, tutti i file sono idonei per la compressione. Il file deve tuttavia essere un tipo MIME [configurato per la compressione](#enabling-compression).
> 
>Questi profili supportano solo la codifica **gzip**. Quando un endpoint del profilo richiede file con codifica **gzip**, i file vengono sempre richiesti dall'origine indipendentemente dalla richiesta del client. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compressione disabilitata o file non idoneo per la compressione
| Formato richiesto del client tramite l'intestazione Accept-Encoding | Formato del file memorizzato nella cache | Risposta della rete CDN al client | Note |
| --- | --- | --- | --- |
| Compresso |Compresso |Compresso | |
| Compresso |Non compresso |Non compresso | |
| Compresso |Non memorizzato nella cache |Compressa o non compressa |A seconda della risposta dell'origine |
| Non compresso |Compresso |Non compresso | |
| Non compresso |Non compresso |Non compresso | |
| Non compresso |Non memorizzato nella cache |Non compresso | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Compressione abilitata e file idoneo per la compressione
| Formato richiesto del client tramite l'intestazione Accept-Encoding | Formato del file memorizzato nella cache | Risposta della rete CDN al client | Note |
| --- | --- | --- | --- |
| Compresso |Compresso |Compresso |Transcodifica della rete CDN tra formati supportati |
| Compresso |Non compresso |Compresso |Compressione da parte della rete CDN |
| Compresso |Non memorizzato nella cache |Compresso |La rete CDN esegue la compressione se l'origine restituisce Non compresso.  **La rete CDN di Azure fornita da Verizon** effettua il passaggio del file non compresso per la prima richiesta, per poi eseguire la compressione e la memorizzazione nella cache del file per le richieste successive.  I file con l'intestazione `Cache-Control: no-cache` non vengono mai compressi. |
| Non compresso |Compresso |Non compresso |Decompressione da parte della rete CDN |
| Non compresso |Non compresso |Non compresso | |
| Non compresso |Non memorizzato nella cache |Non compresso | |

## <a name="media-services-cdn-compression"></a>Compressione della rete CDN dei servizi multimediali
Per gli endpoint di streaming abilitati per la rete CDN di Servizi multimediali, la compressione è abilitata per impostazione predefinita per i tipi di contenuto seguenti: 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

Non è possibile abilitare o disabilitare la compressione per i tipi indicati usando il portale di Azure.  

## <a name="see-also"></a>Vedere anche
* [Risoluzione dei problemi della compressione dei file CDN](cdn-troubleshoot-compression.md)    


