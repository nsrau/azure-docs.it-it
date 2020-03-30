---
title: Domande frequenti - Domande frequenti
titleSuffix: Microsoft Genomics
description: Risposte alle domande comuni relative all'uso del servizio Genomica di Microsoft, incluse informazioni tecniche, contratto di servizio e fatturazione.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986037"
---
# <a name="microsoft-genomics-common-questions"></a>Genomica di Microsoft: Domande comuni

Questo articolo elenca le domande più importanti degli utenti su Genomica di Microsoft. Per ulteriori informazioni sul servizio Genomica di Microsoft, vedere [Che cos'è Genomica di Microsoft?](overview-what-is-genomics.md). Per altre informazioni sulla risoluzione dei problemi, vedere [Guida per la risoluzione dei problemi](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Come si eseguono flussi di lavoro GATK4 in Genomica di Microsoft?
Nel file config.txt del servizio Genomica di `gatk4`Microsoft specificare l'process_name in . Tieni presente che ti verranno addebitati i tassi di fatturazione regolari.

## <a name="how-do-i-enable-output-compression"></a>Come si abilita la compressione dell'output?
È possibile comprimere l'output vcf o gvcf utilizzando un argomento facoltativo per la compressione dell'output. Ciò equivale `-bgzip` all'esecuzione seguita `-tabix` dall'output vcf o `.gz` gvcf, per `.tbi` produrre file (output bgzip) e (output tabix). `bgzip`comprime il file vcf o gvcf e `tabix` crea un indice per il file compresso. L'argomento è un valore `false` booleano, impostato su per `true` impostazione predefinita per l'output vcf e su per impostazione predefinita per l'output gcvf. Per utilizzare nella riga `-bz` di `--bgzip-output` `true` comando, specificare o come (eseguire bgzip e tabix) o `false`. Per utilizzare questo argomento nel file `bgzip_output: true` config.txt, aggiungere o `bgzip_output: false` al file.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Qual è il contratto di servizio per Genomica di Microsoft?
È garantita la disponibilità del servizio Genomica di Microsoft per il 99,9% del tempo a ricevere le richieste di API del flusso di lavoro. Per altre informazioni, vedere [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Come viene indicato l'utilizzo di Genomica di Microsoft in fattura?
Il servizio Genomica di Microsoft effettua la fatturazione in base al numero di gigabase elaborati per flusso di lavoro. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Dove è possibile trovare un elenco di tutti i possibili comandi e argomenti per il client `msgen`?
È possibile ottenere un elenco completo di comandi e argomenti disponibili eseguendo `msgen help`. Se non vengono forniti altri argomenti, mostra un elenco delle sezioni della Guida disponibili, uno per ogni `submit`, `list`, `cancel` e `status`. Per ottenere informazioni su un comando specifico, digitare `msgen help command`; ad esempio `msgen help submit` elenca tutte le opzioni di invio.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quali sono i comandi più comunemente utilizzati per il client `msgen`?
I comandi utilizzati più comunemente sono argomenti per il client `msgen` e includono: 

 |**Comando**          |  **Descrizione campo** |
 |:--------------------|:-------------         |
 |`list`               |Restituisce un elenco di processi inviati. Per gli argomenti, vedere `msgen help list`.  |
 |`submit`             |Invia una richiesta del flusso di lavoro al servizio. Per gli argomenti, vedere `msgen help submit`.|
 |`status`             |Restituisce lo stato del flusso di lavoro specificato da `--workflow-id`. Vedere anche `msgen help status`. |
 |`cancel`             |Invia una richiesta per annullare l'elaborazione del flusso di lavoro specificato da `--workflow-id`. Vedere anche `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Dove si ottiene il valore per `--api-url-base`?
Passare al portale di Azure e aprire la pagina dell'account di Genomica. Nell'intestazione **Gestione**, scegliere **Chiavi di accesso**. Qui sono disponibili sia l'URL dell'API e le chiavi di accesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Dove si ottiene il valore per `--access-key`?
Passare al portale di Azure e aprire la pagina dell'account di Genomica. Nell'intestazione **Gestione**, scegliere **Chiavi di accesso**. Qui sono disponibili sia l'URL dell'API e le chiavi di accesso.

## <a name="why-do-i-need-two-access-keys"></a>Perché occorrono due chiavi di accesso?
Sono necessarie due chiavi di accesso nel caso in cui si desideri aggiornare (rigenerare) le stesse senza interrompere l'utilizzo del servizio. Se, ad esempio, si vuole aggiornare la prima chiave, tutti i nuovi flussi di lavoro dovranno usare la seconda chiave. Prima di aggiornare la prima chiave, attendere quindi che siano completati tutti i flussi di lavoro che usano la prima chiave.

## <a name="do-you-save-my-storage-account-keys"></a>Le chiavi dell'account di archiviazione vengono salvate?
La chiave dell'account di archiviazione viene utilizzata per creare token di accesso a breve termine per permettere al servizio Genomica di Microsoft di leggere i file di input e scrivere i file di output. La durata predefinita dei token è di 48 ore. La durata del token può essere modificata con l'opzione `-sas/--sas-duration` del comando di invio; il valore è espresso in ore.

## <a name="what-genome-references-can-i-use"></a>Quali rierimenti del genoma è possibile usare?

Sono supportati questi riferimenti:

 |Riferimento              | Valore di `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (senza analisi alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Come si formattano gli argomenti della riga di comando come file di configurazione? 

msgen riconosce i file di configurazione nel formato seguente:
* Tutte le opzioni vengono fornite come coppie chiave-valore con i valori separati dalle chiavi da una virgola.
  Lo spazio vuoto viene ignorato.
* Le righe che iniziano con `#` sono ignorate.
* Qualsiasi argomento della riga di comando nel formato lungo può essere convertito in una chiave eliminando i trattini iniziali e sostituendo i trattini tra le parole con caratteri di sottolineatura. Di seguito sono riportati alcuni esempi di conversione:

  |Argomento della riga di comando            | Riga del file di configurazione |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:KEY*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Passaggi successivi

Usare le risorse seguenti per iniziare a usare Genomica di Microsoft:
- Iniziare eseguendo il primo flusso di lavoro tramite il servizio Genomica di Microsoft. [Eseguire un flusso di lavoro tramite il servizio Genomica di Microsoft](quickstart-run-genomics-workflow-portal.md)
- Inviare i propri dati per l'elaborazione dal servizio Genomica di Microsoft: [abbinato FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ o BAM](quickstart-input-multiple.md) 

