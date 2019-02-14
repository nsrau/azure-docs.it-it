---
title: Sperimentazione - Servizio decisionale personalizzato
titlesuffix: Azure Cognitive Services
description: Questo articolo è una guida alla sperimentazione con il Servizio decisionale personalizzato.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b5f8c853218a1db53f4dd23e7254b35990a7132b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870266"
---
# <a name="experimentation"></a>Sperimentazione

Seguendo la teoria dei [contextual bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), il Servizio decisionale personalizzato osserva ripetutamente un contesto, intraprende un'azione e stima il vantaggio per l'azione scelta. Un esempio è la personalizzazione dei contenuti: il contesto descrive un utente, le azioni sono storie candidate e la ricompensa misura quanto l'utente ha gradito la storia consigliata.

Il Servizio decisionale personalizzato produce un criterio, poiché esegue il mapping dai contesti alle azioni. Con criteri di destinazione specifici, si intende conoscere il vantaggio stimato. Un modo per stimare il premio è usare un criterio online e lasciare che scelga le azioni (ad esempio, consigliare storie agli utenti). Tuttavia, tale valutazione online può risultare costosa per due motivi:

* Espone gli utenti a un criterio non testato, sperimentale.
* Non supporta la scalabilità per la valutazione di più criteri di destinazione.

La valutazione fuori criterio è un paradigma alternativo. Se si dispone di log di un sistema online esistente che seguono un criterio di registrazione, la valutazione fuori criterio può stimare i vantaggi previsti dai nuovi criteri di destinazione.

Tramite il file di log, la Sperimentazione cerca di individuare i criteri con il vantaggio più elevato stimato e previsto. I criteri di destinazione vengono parametrizzati dagli argomenti di [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki). Nella modalità predefinita, lo script verifica una serie di argomenti di Vowpal Wabbit accedendo a `--base_command`. Lo script esegue le azioni seguenti:

* Rileva automaticamente gli spazi dei nomi delle funzioni dalle prime righe `--auto_lines` del file di input.
* Esegue un primo sweep sugli iperparametri (`learning rate`, `L1 regularization` e `power_t`).
* Verifica la valutazione dei criteri `--cb_type` (punteggio di propensione inverso (`ips`) o doppiamente solido (`dr`). Per altre informazioni vedere [esempio di Contextual Bandit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Verifica i marginali.
* Verifica le funzioni di interazione quadratica:
   * **fase brute-force**: verifica tutte le combinazioni con coppie di `--q_bruteforce_terms` o un numero inferiore.
   * **fase greedy**: aggiunge la coppia più idonea fino a quando non sono disponibili miglioramenti per i cicli di `--q_greedy_stop`.
* Esegue un secondo sweep sugli iperparametri (`learning rate`, `L1 regularization` e `power_t`).

I parametri che controllano questi passaggi includono alcuni argomenti di Vowpal Wabbit:
- Esempio di opzioni di modifica:
  - spazi dei nomi condivisi
  - spazi dei nomi azione
  - spazi dei nomi marginali
  - funzionalità quadratiche
- Opzioni delle regole di aggiornamento
  - velocità di apprendimento
  - regolarizzazione L1
  - valore potenza t

Per una spiegazione dettagliata degli argomenti precedenti, vedere [gli argomenti della riga di comando di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Prerequisiti
- Vowpal Wabbit: installato e presente nel percorso.
  - Windows: [usare il programma di installazione `.msi`](https://github.com/eisber/vowpal_wabbit/releases).
  - Altre piattaforme: [ottenere il codice sorgente](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: installato e presente nel percorso.
- NumPy: usare la gestione pacchetti di propria scelta.
- Repository *Microsoft/mwt-ds*: [clonare il repository](https://github.com/Microsoft/mwt-ds).
- File di log JSON del servizio decisionale: per impostazione predefinita, il comando base include `--dsjson`, che abilita l'analisi JSON del servizio decisionale del file di dati di input. [Ottenere un esempio di questo formato](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Uso
Passare a `mwt-ds/DataScience` ed eseguire `Experimentation.py` con gli argomenti pertinenti, come indicato in dettaglio nel codice seguente:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Un log dei risultati viene accodato al file *mwt-ds/DataScience/experiments.csv*.

### <a name="parameters"></a>Parametri
| Input | DESCRIZIONE | Predefinito |
| --- | --- | --- |
| `-h`, `--help` | Mostra il messaggio di aiuto ed esce. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Percorso file di dati (formato `.json` o `.json.gz` - ogni riga è un `dsjson`). | Obbligatoria |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Comando base di Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Numero di processi paralleli da usare. | Processori logici |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Spazi dei nomi delle funzioni condivise (ad esempio `abc` corrisponde agli spazi dei nomi `a`, `b` e `c`).  | Rilevamento automatico dal file di dati |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Spazi dei nomi delle funzioni di azione. | Rilevamento automatico dal file di dati |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Spazi dei nomi delle funzioni marginali. | Rilevamento automatico dal file di dati |  
| `--auto_lines AUTO_LINES` | Numero di righe del file di dati da analizzare per rilevare automaticamente gli spazi dei nomi delle funzioni. | `100` |  
| `--only_hp` | Sweep solo su iperparametri (`learning rate`, `L1 regularization` e `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Intervallo di velocità di apprendimento nei valori positivi `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Intervallo di regolarizzazione L1 nei valori positivi `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Intervallo di power_t nei valori positivi `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Numero di coppie quadratiche da sottoporre a test in fase brute-force. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Cicli senza miglioramenti, dopo i quali viene interrotta la fase di ricerca greedy quadratica. | `3` |  

### <a name="examples"></a>Esempi
Per usare i valori predefiniti preimpostati:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

In modo analogo, Vowpal Wabbit può inoltre inserire i file `.json.gz`:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Per effettuare lo sweep solo sugli iperparametri (`learning rate`, `L1 regularization` e `power_t`, fermandosi dopo il passaggio 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
