---
title: "Funzionalità di Azure Cloud Shell (anteprima) | Documentazione Microsoft"
description: "Panoramica delle funzionalità di Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e789283ab9b25f634c50b341ca882bbf9c70a225
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Funzionalità e strumenti per Azure Cloud Shell
Azure Cloud Shell è un'esperienza shell basata su browser per gestire e sviluppare risorse di Azure.

Cloud Shell offre un'esperienza shell preconfigurata accessibile tramite browser per la gestione delle risorse di Azure senza l'onere di dover installare, controllare le versioni e mantenere un computer manualmente.

Cloud Shell esegue il provisioning delle macchine sulla base delle richieste e di conseguenza lo stato della macchina non viene mantenuto tra le sessioni. Poiché Cloud Shell è pensato per le sessioni interattive, le shell vengono terminate automaticamente dopo 10 minuti di inattività della shell stessa.

## <a name="tools"></a>Strumenti
|Categoria   |Nome   |
|---|---|
|Interprete shell di Linux|Bash<br> sh               |
|Strumenti di Azure            |Interfaccia della riga di comando di Azure 2.0 e 1.0     |
|Editor di testo           |vim<br> nano<br> emacs       |
|Controllo del codice sorgente         |git                    |
|Strumenti di compilazione            |make<br> maven<br> npm<br> pip         |
|Contenitori             |Docker<br> Kubectl<br> Interfaccia della riga di comando DC/OS         |
|Database              |Client MySQL<br> Client PostgreSql<br> Utilità sqlcmd      |
|Altri                  |Client iPython |

## <a name="language-support"></a>Supporto per le lingue
|Lingua   |Versione   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 e 3.5|

## <a name="secure-automatic-authentication"></a>Autenticazione automatica sicura
Cloud Shell autentica in modo sicuro e automatico l'accesso agli account per l'interfaccia della riga di comando di Azure 2.0.

## <a name="azure-files-persistence"></a>Persistenza di File di Azure
Poiché Cloud Shell viene allocato su base richiesta utilizzando un computer temporaneo, i file locali esterni a $Home e lo stato del computer non sono mantenuti tra le sessioni.
Per rendere persistenti i file fra le sessioni, Cloud Shell illustra come associare una condivisione file di Azure al primo avvio.
Al termine, Cloud Shell assocerà automaticamente lo spazio di archiviazione per tutte le sessioni future.

[Altre informazioni sul collegamento delle condivisioni di file di Azure a Cloud Shell. ](persisting-shell-storage.md).

## <a name="next-steps"></a>Passaggi successivi
[Guida introduttiva a Cloud Shell](quickstart.md) 
[Informazioni sull'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/)
