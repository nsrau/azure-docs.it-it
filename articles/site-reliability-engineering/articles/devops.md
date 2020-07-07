---
title: 'Domande frequenti: SRE e DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Domande frequenti: informazioni sulla relazione tra SRE e DevOps'
author: dnblankedelman
manager: efreeman
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 04/22/2020
ms.author: dnb
ms.openlocfilehash: e917c609b484b1a58377fea2f6cdd75dde30ca6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82196413"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Domande frequenti: qual è la relazione tra SRE e DevOps?

È disponibile una serie di domande comuni che derivano dalla relazione tra la progettazione dell'affidabilità del sito e DevOps, tra cui "come sono uguali? ma presentano alcune differenze. Possiamo avere entrambe le nostre organizzazioni? ". Questo articolo tenta di condividere alcune delle risposte offerte dalle community SRE e DevOps che ci avvicinano a una conoscenza di questa relazione.

## <a name="how-are-they-the-same"></a>Come sono uguali?

SRE e DevOps sono procedure di operazioni moderne create e sviluppate in risposta a problemi che includono:

- una maggiore complessità degli ambienti di produzione e dei processi di sviluppo
- maggiore dipendenza aziendale dal funzionamento continuo di tali ambienti
- l'impossibilità di ridimensionare la forza lavoro in modo lineare con le dimensioni di questi ambienti
- la necessità di muoversi più velocemente mantenendo comunque la stabilità operativa

Entrambe le procedure operative valutano l'attenzione per gli argomenti cruciali per affrontare tali problemi, ad esempio monitoraggio/osservabilità, automazione, documentazione e strumenti di sviluppo software collaborativi.

Si verifica una notevole sovrapposizione di strumenti e aree di lavoro tra SRE e DevOps. Quando _la cartella di lavoro di affidabilità del sito_ lo inserisce, "SRE crede come DevOps, ma per motivi leggermente diversi".

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Tre diversi modi per confrontare le due procedure operative

Le analogie tra SRE e DevOps sono chiare. Dove diventa molto interessante è il modo in cui i due sono diversi o divergenti. Qui sono disponibili tre modi per considerare la relazione come un modo per fornire alcune sfumature a questa domanda. È possibile che non si accettino queste risposte, ma ognuna fornisce un valido punto di partenza per la discussione.

### <a name="class-sre-implements-interface-devops"></a>"classe SRE implementa l'interfaccia DevOps"

_La cartella di lavoro di affidabilità del sito_ (indicata nell' [elenco dei libri di risorse](../resources/books.md)) illustra SRE e DevOps nel primo capitolo. In tale capitolo viene utilizzata la frase "classe SRE implementa l'interfaccia DevOps" come sottotitolo. Questo è pensato per suggerire (usando una frase destinata agli sviluppatori) che SRE potrebbe essere considerata un'implementazione specifica della filosofia DevOps. Come fa riferimento il capitolo "DevOps è relativamente silenzioso su come eseguire le operazioni a un livello dettagliato" mentre SRE è molto più proscrittivo nelle procedure. Quindi, una possibile risposta alla domanda su come le due relazioni sono le seguenti, potrebbe essere considerata una delle numerose possibili implementazioni di DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE è l'affidabilità perché il recapito è DevOps

Questo confronto è un po' infangato perché sono presenti più definizioni per SRE e DevOps, ma è comunque potenzialmente utile. Si inizia con la domanda "se è necessario ricavare ogni pratica delle operazioni in una o due parole che riflettono la preoccupazione principale, cosa sarebbe?"

Se si usa questa definizione di SRE dall' [Hub Engineering di affidabilità del sito](../index.yml):

> Site Reliability Engineering (SRE) è una disciplina di ingegneria informatica dedicata ad assistere le organizzazioni nell'ottenimento in modo sostenibile dei livelli di affidabilità appropriati per i sistemi, i servizi e i prodotti.

quindi, sarebbe facile dire che la parola per SRE è "affidabilità". Il fatto di trovarsi all'interno del nome offre anche un'ottima evidenza per questa richiesta.

Se si usa questa definizione di DevOps dal [Centro risorse di Azure DevOps](https://docs.microsoft.com/azure/devops/learn/):

> DevOps è l'unione di persone, processi e prodotti per offrire recapito continuo di valore agli utenti finali.

una distillazione simile per DevOps potrebbe essere "recapito".

"SRE è quindi un'affidabilità come la recapito DevOps".

### <a name="direction-of-attention"></a>Direzione di attenzione

Questa risposta è racchiusa tra virgolette o leggermente parafrasi da un contributo di Thomas limoncelli al libro _SRE di ricerca_ menzionato nell' [elenco di libri delle risorse](../resources/books.md). Sa che i tecnici di DevOps si concentrano principalmente sulla pipeline del ciclo di vita dello sviluppo software con responsabilità occasionali delle operazioni di produzione, mentre SRE si concentrano sulle operazioni di produzione con responsabilità occasionali della pipeline SDLC.

Tuttavia, ancora più importante, disegna anche un diagramma che inizia con il processo di sviluppo del software su un lato e le operazioni di produzione funzionano sull'altro. I due sono connessi tramite la pipeline usuale compilata per portare il codice da uno sviluppatore, Shepherd it attraverso il numero desiderato di test e fasi, quindi spostare tale codice nell'ambiente di produzione.

Limoncelli nota che i tecnici DevOps iniziano nell'ambiente di sviluppo e automatizzano i passaggi per la produzione. Al termine, tornano a ottimizzare i colli di bottiglia.

SRE, d'altra parte, si concentrano sulle operazioni di produzione e raggiungono la pipeline in modo approfondito come mezzo per migliorare il risultato finale (fondamentalmente funzionando nella direzione opposta).

Si tratta di una differenza nella direzione dell'obiettivo di SRE e DevOps che può essere utile per distinguerli.

## <a name="coexistence-in-the-same-organization"></a>Coesistenza nella stessa organizzazione

La domanda finale da affrontare è "è possibile avere sia SRE che DevOps nella stessa organizzazione?"

La risposta a questa domanda è un'enfasi "Sì!".

Ci auguriamo che le risposte precedenti offrano una certa idea del modo in cui le due procedure operative si sovrappongono e, quando non si sovrappongono, come possono essere complementari nello stato attivo. Le organizzazioni con una prassi DevOps stabilita possono sperimentare le pratiche di SRE su scala ridotta (ad esempio, provando SLIs e SLOs) senza dover impegnarsi nella creazione di posizioni o team di SRE. Si tratta di un modello di adozione SRE abbastanza comune.

## <a name="next-steps"></a>Passaggi successivi

Vuoi saperne di più sulla progettazione dell'affidabilità del sito o DevOps? Vedere l' [Hub progettazione dell'affidabilità del sito](../index.yml) e il [Centro risorse di Azure DevOps](https://docs.microsoft.com/azure/devops/learn/).
