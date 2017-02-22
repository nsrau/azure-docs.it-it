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
translationtype: Human Translation
ms.sourcegitcommit: 57d00f2192fed7a2e89ac94e110ebb7e84c83b72
ms.openlocfilehash: e80136d096ba83ab5050c8d1d95a9e2abb7a3646


---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure
La compressione è un metodo semplice ed efficace per aumentare la velocità di trasferimento dei file e migliorare le prestazioni di caricamento delle pagine mediante la riduzione delle dimensioni del file prima che venga inviato dal server. Riduce i costi della larghezza di banda e offre un'esperienza più reattiva per gli utenti.

Esistono due modi per abilitare la compressione:

* È possibile abilitare la compressione nel server di origine, nel qual caso la rete CDN eseguirà il pass-through dei file compressi e distribuirà i file compressi ai client che li hanno richiesti.
* È possibile abilitare la compressione direttamente sui server perimetrali della rete CDN. In questo caso la rete CDN comprimerà i file e li trasmetterà agli utenti finali anche se non sono stati compressi dal server di origine.

> [!IMPORTANT]
> Le modifiche alla configurazione della rete CDN richiedono tempo per propagarsi attraverso la rete.  La propagazione dei profili della <b>Rete CDN di Azure fornita da Akamai</b> in genere dura meno di un minuto.  La propagazione dei profili della <b>Rete CDN di Azure fornita da Verizon</b> , in genere le modifiche vengono applicate entro 90 minuti.  Se questa è la prima volta che si configura la compressione per l'endpoint della rete CDN, è consigliabile attendere 1-2 ore per assicurarsi che le impostazioni di compressione si siano propagate ai POP prima di intraprendere la risoluzione di eventuali problemi.
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

1. Dal pannello del profilo di rete CDN, fare clic sull'endpoint della rete CDN che si desidera gestire.
   
    ![Endpoint del pannello del profilo di rete CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    Viene visualizzato il pannello di endpoint della rete CDN.
2. Fare clic sul pulsante **Configura** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    Si apre il pannello di configurazione della rete CDN.
3. Attivare **compressione**.
   
    ![Opzioni di compressione della rete CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Usare i tipi predefiniti, o modificare l'elenco eliminando o aggiungendo tipi di file.
   
   > [!TIP]
   > Se possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4, JPG e così via.
   > 
   > 
5. Dopo aver apportato le modifiche, fare clic sul pulsante **Salva** .

### <a name="premium-tier"></a>Livello Premium
> [!NOTE]
> Questa sezione si applica ai profili di **Rete CDN Premium di Azure da Verizon** .
> 
> 

1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci** .
   
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Si aprirà il portale di gestione della rete CDN.
2. Passare il puntatore sulla scheda **HTTP Large** (HTTP esteso) e quindi sul riquadro a comparsa **Impostazioni cache**.  Fare clic su **Compressione**.
   
    Vengono visualizzate le opzioni di compressione.
   
    ![Compressione dei file](./media/cdn-file-compression/cdn-compress-files.png)
3. Abilitare la compressione facendo clic sul pulsante di opzione **Compressione abilitata** .  Immettere i tipi MIM da comprimere sotto forma di elenco delimitato da virgole, senza spazi, nella casella di testo **Tipi di file** .
   
   > [!TIP]
   > Se possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4, JPG e così via. 
   > 
   > 
4. Dopo aver apportato le modifiche, fare clic sul pulsante **Aggiorna** .

## <a name="compression-rules"></a>Regole di compressione
Le tabelle seguenti descrivono il comportamento della compressione della rete CDN di Azure per ogni scenario.

> [!IMPORTANT]
> Per la **Rete CDN di Azure fornita da Verizon** (Standard e Premium) vengono compressi soltanto i file idonei.  Per essere idoneo per la compressione, un file deve essere:
> 
> * Maggiore di 128 byte.
> * Minore di 1 MB.
> 
> Per la **Rete CDN di Azure fornita da Akamai**, tutti i file sono idonei per la compressione.
> 
> Per tutti i prodotti della rete CDN di Azure, il file deve essere un file di tipo MIME [configurato per la compressione](#enabling-compression).
> 
> I profili della **rete CDN di Azure fornita da Verizon** (Standard e Premium) supportano la codifica **gzip**, **deflate** o **bzip2**.  I profili della **rete CDN di Azure fornita da Akamai** supportano solo la codifica **gzip**.
> 
> Gli endpoint della **rete CDN di Azure fornita da Akamai** richiedono sempre file con codifica **gzip** dall'origine, indipendentemente dalla richiesta del client.
> 
> 

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
| Compresso |Non memorizzato nella cache |Compresso |La rete CDN esegue la compressione se l'origine restituisce Non compresso.  **La rete CDN di Azure da Verizon** effettuerà il passaggio del file non compresso per la prima richiesta, per poi eseguire la compressione e la memorizzazione nella cache del file per le richieste successive.  I file con l'intestazione `Cache-Control: no-cache` non verranno mai compressi. |
| Non compresso |Compresso |Non compresso |Decompressione da parte della rete CDN |
| Non compresso |Non compresso |Non compresso | |
| Non compresso |Non memorizzato nella cache |Non compresso | |

## <a name="media-services-cdn-compression"></a>Compressione della rete CDN dei servizi multimediali
Per endpoint di streaming abilitati alla rete CDN dei servizi multimediali, la compressione è abilitata per impostazione predefinita per i seguenti tipi di contenuto: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Non è possibile attivare o disattivare la compressione per i tipi indicati tramite il portale di Azure.  

## <a name="see-also"></a>Vedere anche
* [Risoluzione dei problemi della compressione dei file CDN](cdn-troubleshoot-compression.md)    




<!--HONumber=Jan17_HO4-->


