---
title: 'Microsoft Genomics: Domande frequenti | Documenti Microsoft'
titleSuffix: Azure
description: Le risposte ai clienti domande comuni porre su Genomics Microsoft.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 2077eeb5177b07c458476ae900f81b72e35f0dc3
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Domande frequenti

Questo articolo vengono elencati nella parte superiore query che potrebbe essere correlati a Microsoft Genomics. Per ulteriori informazioni sul servizio Genomics Microsoft, vedere [che cos'è Microsoft Genomics?](overview-what-is-genomics.md) 


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Che cos'è il contratto di servizio per Microsoft Genomics?
È garantito che al 99,9% del servizio ora di Microsoft Genomics sarà disponibile a ricevere le richieste API di flusso di lavoro. Per altre informazioni, vedere [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>La modalità l'utilizzo di Microsoft Genomics vengono visualizzati nella fattura?
Microsoft Genomics distinte in base al numero di gigabases elaborati al flusso di lavoro. Per ulteriori informazioni, vedere [prezzi](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Dove posso trovare un elenco di tutti i possibili comandi e argomenti per il `msgen` client?
È possibile ottenere un elenco completo di comandi disponibili e gli argomenti eseguendo `msgen help`. Se ulteriori argomenti non vengono fornite, Mostra un elenco di argomenti della Guida disponibile sezioni, uno per ogni `submit`, `list`, `cancel`, e `status`. Per ottenere informazioni su un comando specifico, digitare `msgen help command`, ad esempio `msgen help submit` Elenca tutte le opzioni di invio.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Quali sono più comunemente utilizzati i comandi per il `msgen` client?
I comandi utilizzati più di frequente sono argomenti per il `msgen` client includono: 

 |**Comando**          |  **Descrizione campo** |
 |:--------------------|:-------------         |
 |`list`               |Restituisce un elenco di processi che è stata inviata. Per argomenti, vedere `msgen help list`.  |
 |`submit`             |Invia una richiesta del flusso di lavoro per il servizio. Per argomenti, vedere `msgen help submit`.|
 |`status`             |Restituisce lo stato del flusso di lavoro specificato da `--workflow-id`. Vedere anche `msgen help status`. |
 |`cancel`             |Invia una richiesta per annullare l'elaborazione del flusso di lavoro specificato da `--workflow-id`. Vedere anche `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Dove ottenere il valore per `--api-url-base`?
Passare al portale Azure e aprire la pagina dell'account genomica. Sotto il **Management** titolo, scegliere **le chiavi di accesso**. Non esiste, trovare l'URL di API e le chiavi di accesso.

## <a name="where-do-i-get-the-value-for---access-key"></a>Dove ottenere il valore per `--access-key`?
Passare al portale Azure e aprire la pagina dell'account genomica. Sotto il **Management** titolo, scegliere **le chiavi di accesso**. Non esiste, trovare l'URL di API e le chiavi di accesso.

## <a name="why-do-i-need-two-access-keys"></a>Motivi di due chiavi di accesso
Nel caso in cui si desidera aggiornare (regenerate), è necessario due chiavi di accesso loro senza interrompere l'utilizzo del servizio. Ad esempio, si desidera aggiornare la prima chiave. In tal caso, si passa tutti i nuovi flussi di lavoro per usare la seconda chiave. Attendere finché non vengono completati i flussi di lavoro già in esecuzione utilizzando la prima chiave. Solo a questo punto, aggiornare la chiave.

## <a name="do-you-save-my-storage-account-keys"></a>Si salva la chiavi dell'account di archiviazione?
La chiave di account di archiviazione viene utilizzata per creare token di accesso a breve termine per il servizio Microsoft Genomics leggere i file di input e scrivere i file di output. La durata del token predefinito è 48 ore. La durata del token può essere modificata con il `-sas/--sas-duration` opzione del comando Invia; il valore è espresso in ore.

## <a name="what-genome-references-can-i-use"></a>Quali genoma fa riferimento è possibile usare?

Questi riferimenti sono supportati:
 |Riferimenti              | Valore di`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (Nessuna analisi alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Come formattare gli argomenti della riga di comando come un file di configurazione? 

msgen riconosce i file di configurazione nel formato seguente:
* Tutte le opzioni vengono fornite come coppie chiave-valore con valori separati da chiavi da un virgola.
Gli spazi vuoti viene ignorato.
* Le righe a partire da `#` vengono ignorati.
* Qualsiasi argomento della riga di comando nel formato lungo può essere convertito in una chiave di rimozione dei trattini relativo iniziali e sostituendo i trattini tra le parole con caratteri di sottolineatura. Di seguito sono riportati alcuni esempi di conversione:

 |Argomento della riga di comando            | Riga del file di configurazione |
 |:-------------                   |:-------------                 |
 |`-u/--api-url-base https://url`  | *api_url_base:https://URL*    |
 |`-k/--access-key KEY`            | *access_key:Key*              |      
 |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Passaggi successivi

Usare le risorse seguenti per iniziare a utilizzare Microsoft Genomics:
- Iniziare eseguendo il primo flusso di lavoro tramite il servizio Microsoft Genomics. [Eseguire un flusso di lavoro tramite il servizio Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Invio dei dati per l'elaborazione dal servizio Microsoft Genomics: [abbinato FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [più FASTQ o BAM](quickstart-input-multiple.md) 

