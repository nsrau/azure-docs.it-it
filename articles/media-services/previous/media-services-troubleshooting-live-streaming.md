---
title: Guida per la risoluzione dei problemi relativi allo streaming live | Microsoft Docs
description: Questo argomento offre suggerimenti per la risoluzione dei problemi relativi allo streaming live.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544695"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Guida per la risoluzione dei problemi relativi allo streaming live  

Questo articolo offre suggerimenti per la risoluzione di alcuni problemi relativi allo streaming live.

## <a name="issues-related-to-on-premises-encoders"></a>Problemi relativi ai codificatori locali
In questa sezione sono disponibili suggerimenti su come risolvere i problemi relativi ai codificatori locali configurati per l'invio di un flusso a velocità in bit singola ai canali AMS abilitati per la codifica live.

### <a name="problem-would-like-to-see-logs"></a>Problema: i log non sono reperibili
* **Potenziale problema**: non è possibile trovare i log del codificatore che potrebbero essere utili per il debug dei problemi.
  
  * **Telestream Wirecast**: in genere è possibile trovare i log in C:\Utenti\{nome utente}\AppData\Roaming\Wirecast\ 
  * **Elemental Live**: è possibile trovare i collegamenti ai log nel portale di gestione. Fare clic su **Stats**, quindi su **Logs**. Nella pagina **Log Files** verrà visualizzato un elenco di log per tutti gli elementi LiveEvent. Selezionare quello corrispondente alla sessione corrente. 
  * **Flash Media Live Encoder**: per trovare **Log Directory** (Directory log), passare alla scheda **Encoding Log** (Log codifica).

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problema: non esiste alcuna opzione per l'output di uno streaming progressivo
* **Potenziale problema**: il codificatore usato non esegue automaticamente il deinterlacciamento. 
  
    **Passaggi per la risoluzione dei problemi**: cercare un'opzione di deinterlacciamento nell'interfaccia del codificatore. Dopo aver abilitato il deinterlacciamento, verificare di nuovo se sono disponibili impostazioni per l'output progressivo. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problema: dopo aver provato varie impostazioni di output per il codificatore, è ancora impossibile connettersi.
* **Potenziale problema**: il canale di codifica di Azure non è stato reimpostato correttamente. 
  
    **Passaggi per la risoluzione dei problemi**: verificare che il codificatore non esegua più il push ad AMS e quindi arrestare e reimpostare il canale. Quando è di nuovo in esecuzione, provare a connettere il codificatore con le nuove impostazioni. Se il problema persiste, provare a creare un canale del tutto nuovo. A volte i canali possono risultare danneggiati dopo diversi tentativi non riusciti.  
* **Potenziale problema**: le dimensioni GOP o le impostazioni del fotogramma chiave non sono ottimali. 
  
    **Passaggi per la risoluzione dei problemi**: l'intervallo consigliato per le dimensioni GOP o il fotogramma chiave è di due secondi. Alcuni codificatori calcolano questa impostazione come numero di fotogrammi, mentre altri usano i secondi. Ad esempio: per l'output di 30 fps, le dimensioni GOP sarebbero di 60 fotogrammi, equivalenti a 2 secondi.  
* **Potenziale problema**: le porte chiuse bloccano lo streaming. 
  
    **Passaggi per la risoluzione dei problemi**: durante lo streaming tramite RTMP, controllare le impostazioni del firewall e/o del proxy per assicurarsi che le porte in uscita 1935 e 1936 siano aperte. 

> [!NOTE]
> Se dopo aver seguito le procedure di risoluzione dei problemi non è ancora possibile eseguire correttamente il flusso, inviare un ticket di supporto tramite il portale di Azure.
> 
> 

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

