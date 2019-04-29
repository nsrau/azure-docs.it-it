---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756199"
---
|     Codice di errore     |      Descrizione dell'errore     |
|--------------------|--------------------------|
|    100             | Il nome del contenitore o della condivisione deve avere una lunghezza compresa tra 3 e 63 caratteri.|
|    101             | Il nome del contenitore o della condivisione deve contenere solo lettere, numeri o trattini.|
|    102             | Il nome del contenitore o della condivisione deve contenere solo lettere, numeri o trattini.|
|    103             | Il nome del BLOB o del file contiene caratteri di controllo non supportati.|
|    104             | Il nome del BLOB o del file contiene caratteri illegali.|
|    105             | Il nome del BLOB o del file contiene troppi segmenti (ciascun segmento è separato da una barra, /).|
|    106             | Il nome del BLOB o del file è troppo lungo.|
|    107             | Uno dei segmenti nel nome del blob o del file è troppo lungo. |
|    108             | Le dimensioni del file superano quelle massime consentite per il caricamento.    |
|    109             | Il blob o file è allineato in modo errato.  |
|    110             | Il nome file o BLOB con codifica Unicode non è valido.|
|    111             | Il nome o il prefisso del file o del BLOB è un nome riservato che non è supportato (ad esempio, COM1).|
|    2000            | Una mancata corrispondenza degli Etag indica che si verifica un conflitto tra un BLOB in blocchi nel cloud e nel dispositivo. Per risolvere questo conflitto, eliminare uno di questi file, ovvero la versione nel cloud o la versione nel dispositivo.    |
|    2001            | Si è verificato un problema imprevisto durante l'elaborazione di un file dopo il caricamento.    Se viene visualizzato questo errore e l'errore persiste per oltre 24 ore, contattare il supporto tecnico. |
|    2002            | Il file è già aperto in un altro processo e non sarà possibile caricarlo fino alla chiusura dell'handle.|
|    2003            | Non è stato possibile aprire il file per il caricamento. Se viene visualizzato questo errore, contattare il supporto tecnico Microsoft.|
|    2004            | Non è stato possibile connettersi al contenitore per il caricamento dei dati.|
|    2005            | Non è stato possibile connettersi al contenitore perché le autorizzazioni dell'account sono errate o non aggiornate. Controllare l'accesso.|
|    2006            | Non è stato possibile caricare i dati nell'account perché l'account o la condivisione sono disabilitati.|
|    2007            | Non è stato possibile connettersi al contenitore perché le autorizzazioni dell'account sono errate o non aggiornate. Controllare l'accesso.|
|    2008            | Non è stato possibile aggiungere nuovi dati perché il contenitore è pieno. Verificare le specifiche di Azure per le dimensioni del contenitore supportate in base al tipo. Ad esempio, File di Azure supporta solo dimensioni massime di 5 TB.|
|    2009            | Non è stato possibile caricare i dati perché il contenitore associato alla condivisione non esiste.|    
|    2997            | Si è verificato un errore imprevisto. Si tratta di un errore temporaneo che si risolverà automaticamente.|
|    2998            | Si è verificato un errore imprevisto. L'errore potrebbe risolversi da solo, ma se persiste per oltre 24 ore, contattare il supporto tecnico Microsoft.|
|    16000           | Non è stato possibile trasferire questo file.|
|    16001           | Non è stato possibile trasferire questo file perché è già presente nel sistema locale.|
|    16002           |Non è stato possibile aggiornare questo file perché non è completamente caricato.|

