---
title: 'Introduzione: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: "Questa è una panoramica più approfondita che descrive Threat Modeling Tool in azione."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 2d940b42108948f4cd36a585f1e79def05fe8fd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Guida introduttiva a Threat Modeling Tool

Il team degli strumenti di sicurezza di cloud e aziendali ha rilasciato l'anteprima di Threat Modeling Tool quest'anno come **[download](https://aka.ms/tmtpreview)** gratuito. Il cambiamento nel meccanismo di recapito consente di effettuare il push dei miglioramenti e le correzioni di bug più recenti verso i clienti ogni volta che aprono lo strumento, semplificandone la gestione e l'utilizzo.
Questo articolo illustra il processo di introduzione all'approccio di modellazione Microsoft SDL e illustra come utilizzare lo strumento per sviluppare eccellenti modelli di rischio come base del processo di protezione.

Questo articolo si basa sulle conoscenze esistenti dell'approccio di modellazione delle minacce SDL. Per una rapida panoramica, vedere **[Threat Modeling Web Applications](https://msdn.microsoft.com/library/ms978516.aspx)** (Modellazione delle minacce per le applicazioni Web) e la versione archiviata dell'articolo **[Uncover Security Flaws Using the STRIDE Approach](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** (Scoprire le falle della sicurezza mediante l'approccio STRIDE) di MSDN pubblicato nel 2006.

Per riepilogare rapidamente, l'approccio prevede la creazione di un diagramma, l'identificazione delle minacce, la loro mitigazione e la convalida di ogni mitigazione. Di seguito è riportato un diagramma che illustra questo processo:

![Processo SDL](./media/azure-security-threat-modeling-tool/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Avvio del processo di modellazione delle minacce

Quando si avvia Threat Modeling Tool, si noteranno alcune cose, come illustrato nell'immagine:

![Pagina iniziale vuota](./media/azure-security-threat-modeling-tool/tmtstart.png)

### <a name="threat-model-section"></a>Sezione del modello di minaccia

| Componente                                   | Dettagli                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Pulsante Commenti, Suggerimenti e Problemi** | Consente di passare al **[Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** per tutti gli aspetti di SDL. Fornisce la possibilità di leggere ciò che altri utenti stanno facendo, insieme a consigli e raccomandazioni. Se ancora non si trova ciò che sta cercando, inviare un messaggio di posta elettronica a tmtextsupport@microsoft.com per ottenere l'aiuto del team di supporto                                                                                                                            |
| **Creare un modello**                          | Apre un canvas vuoto in cui disegnare il diagramma. Selezionare il modello di partenza che si desidera usare per la modellazione                                                                                                                                                                                                                                                                                                                                                                       |
| **Modello di base per la modellazione**                 | È necessario selezionare il modello di partenza da usare prima di procedere alla modellazione. Il nostro modello di partenza principale è Azure Threat Model, che contiene gli stencil, minacce e mitigazioni specifici di Azure. Per i modelli generici, selezionare la Knowledge Base TM SDL dal menu a discesa. Se si desidera creare un proprio modello di partenza o inviarne uno nuovo per tutti gli utenti, vedere la nostra pagina GitHub con il **[repository dei modelli di base](https://github.com/Microsoft/threat-modeling-templates)** per altre informazioni                              |
| **Aprire un modello**                            | <p>Vengono aperti modelli di minacce salvati in precedenza. La funzionalità modelli aperti di recente è molto utile se è necessario aprire i file più recenti. Quando si passa il mouse sulla selezione, sono disponibili 2 modi per aprire i modelli:</p><p><ul><li>Apertura dal computer in uso: un modo classico per aprire un file utilizzando l'archiviazione locale</li><li>Apertura da OneDrive: i team possono usare cartelle in OneDrive per salvare e condividere tutti i loro modelli di minacce in un'unica posizione per migliorare la produttività e la collaborazione</li></ul></p> |
| **Guida introduttiva**                   | Si apre la pagina principale **[Microsoft Threat Modeling Tool](./azure-security-threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Sezione del modello di partenza

| Componente               | Dettagli                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Crea nuovo modello** | Apre un modello vuoto da cui iniziare. Se non si dispone di conoscenze approfondite per la creazione di modelli da zero, si consiglia di partire da quelli esistenti |
| **Apri modello**       | Apre modelli esistente che è possibile modificare                                                                                                              |

Il team di Threat Modeling Tool lavora costantemente per migliorare le funzionalità e l'esperienza dello strumento. Lievi modifiche potrebbero essere apportate nel corso dell'anno, ma tutte le modifiche importanti richiedono la riscrittura della guida. Si consiglia di consultarla con frequenza per conoscere gli annunci più recenti.

## <a name="building-a-model"></a>Creazione di un modello

In questa sezione si seguiranno:

- Cristina (uno sviluppatore)
- Ricardo (un program manager) e
- Ashish (un tester)

Essi stanno completando il processo di sviluppo del loro primo modello di minaccia.

> Ricardo: Ciao Cristina, ho lavorato al diagramma del modello di minaccia e volevo assicurarmi che i dettagli siano a posto. Puoi darmi una mano a controllare?
> Cristina: Certo. Vediamo.
> Ricardo apre lo strumento e condivide la sua schermata con Cristina.

![Modello di minaccia di base](./media/azure-security-threat-modeling-tool/basictmt.png)

> Cristina: OK, sembra semplice, ma puoi spiegarmelo?
> Ricardo: Certo! Ecco i dettagli:
> - L'utente è disegnato come un'entità esterna: un quadrato
> - Sta inviando comandi al nostro server Web: il cerchio
> - Il server Web sta consultando un database (due linee parallele)

Ciò che Ricardo ha appena mostrato a Cristina è un DFD, abbreviazione di **[diagramma di flusso dei dati](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Threat Modeling Tool consente agli utenti di specificare i limiti di trust, indicati dalle linee rosse tratteggiate, per mostrare dove entità diverse sono sotto controllo. Ad esempio, gli amministratori IT richiedono un sistema Active Directory per scopi di autenticazione, perciò Active Directory è fuori dal loro controllo.

> Cristina: Mi sembra perfetto. E le minacce?
> Ricardo: Ti mostro.

## <a name="analyzing-threats"></a>Analisi delle minacce

Quando fa clic sulla visualizzazione di analisi nel menu di icone (il file con la lente di ingrandimento), compare un elenco delle minacce generate trovare da Threat Modeling Tool in base al modello predefinito che usa l'approccio SDL chiamato **[STRIDE (Spoofing, Tampering, Info Disclosure, Denial of Service and Elevation of Privilege)](https://en.wikipedia.org/wiki/STRIDE_(security))**. L'idea è che il software è soggetto a una serie di minacce prevedibili che si possono trovare usando queste 6 categorie.

Questo approccio è come proteggere una casa dotando ogni porta e finestra di un meccanismo di blocco prima di aggiungere un sistema di allarme o rincorrere il ladro.

![Minacce di base](./media/azure-security-threat-modeling-tool/basicthreats.png)

Ricardo inizia selezionando il primo elemento nell'elenco. Di seguito è illustrato ciò che accade:

In primo luogo è stata migliorata l'interazione tra i due stencil

![Interazione](./media/azure-security-threat-modeling-tool/interaction.png)

In secondo luogo, altre informazioni sulla minaccia sono visualizzate nella finestra delle proprietà della minaccia

![Informazioni di interazione](./media/azure-security-threat-modeling-tool/interactioninfo.png)

La minaccia generata lo aiuta a comprendere i potenziali difetti di progettazione. La classificazione STRIDE gli dà un'idea dei potenziali vettori di attacco, mentre la descrizione aggiuntiva gli dice esattamente qual è il problema, unitamente a possibili modi per limitarlo. Può usare campi modificabili per scrivere note nei dettagli di giustificazione o modificare le classificazioni di priorità a seconda della barra di bug della propria organizzazione.

I modelli di Azure hanno informazioni aggiuntive per consentire agli utenti di comprendere non solo qual è il problema ma anche come risolverlo aggiungendo descrizioni, esempi e collegamenti ipertestuali alla documentazione specifica di Azure.

La descrizione gli ha permesso di capire l'importanza di aggiungere un meccanismo di autenticazione per impedire agli utenti di subire spoofing, rivelando la prima minaccia su cui lavorare. Dopo alcuni minuti di discussione con Cristina, capiscono l'importanza di implementare il controllo di accesso e i ruoli. Ricardo compila alcune brevi note per assicurarsi che tutto questo sia implementato.

Non appena Ricardo entra nelle minacce nella sezione di divulgazione delle informazioni, si accorge che il piano di controllo di accesso necessario richiede alcuni account di sola lettura per il controllo e la generazione di report. Si chiede se questa dovrebbe essere una nuova minaccia, ma le mitigazioni sono le stesse, perciò annota la minaccia di conseguenza.
Pernsa ancora un po' alla divulgazione di informazioni e si accorge che i nastri di backup richiederanno la crittografia, un lavoro per il team addetto alle operazioni.

Le minacce non applicabili alla progettazione a causa di mitigazioni o garanzie di sicurezza esistenti possono essere modificate in "Non applicabile" dall'elenco a discesa Stato. Ci sono tre altre opzioni: ovvero Non avviato, la selezione predefinita, Richiede analisi, che consente di seguire gli elementi, e Mitigato, per quando l'elaborazione è stata completata.

## <a name="reports--sharing"></a>Report e condivisione

Dopo che Ricardo ha ripercorso l'elenco con Cristina e aggiunto note importanti, mitigazioni/giustificazioni, modifiche di priorità e stato, seleziona Report -> Crea report completo -> Salva report, per stampare un bel report da usare insieme ai collegji come traccia per assicurarsi che gli interventi di sicurezza appropriati siano eseguiti.

![Informazioni di interazione](./media/azure-security-threat-modeling-tool/report.png)

Se invece Ricardo desidera condividere il file, può farlo facilmente salvandolo nell'account OneDrive dell'organizzazione. Dopo averlo fatto, può copiare il collegamento al documento e condividerlo con i colleghi. 

## <a name="threat-modeling-meetings"></a>Riunioni per la modellazione delle minacce

Quando Ricardo invia il suo modello di minaccia ai colleghi con OneDrive, Ashish, il tester, è un po' deluso. Apparentemente Ricardo e Cristina hanno trascurato alcune aree importanti che potrebbero essere compromessi facilmente. Il suo scetticismo fa da complemento ai modelli di minacce.

In questo scenario, dopo che Ashish riceve il modello di minaccia chiede due rinuoni per la modellazione delle minacce: una riunione per sincronizzarsi sul processo ed esaminare i diagrammi e quindi una seconda riunione per la revisione della minaccia e l'approvazione.

Nella prima riunione Ashish dedica 10 minuti a ripercorrere insieme a tutti gli altri il processo di modellazione delle minacce SDL. Quindi prende il diagramma del modello di minaccia e inizia a spiegarlo nei dettagli. Entro cinque minuti viene individuato un importante componente mancante.

Alcuni minuti dopo, Ashish e Ricardo iniziano un'analisi approfondita del modo in cui è costruito il server Web. Non è il modo ideale per condurre una riunione, ma tutti alla fine concordato infine che l'individuazione tempestiva della discrepanza li salverà in futuro.

Nella seconda riunione il team esamina in dettaglio le minacce, discute di alcuni modi per risolverle e ragiunge l'accordo sul modello di minaccia. Passano il documento al controllo del codice sorgente e continuano con lo sviluppo.

## <a name="thinking-about-assets"></a>Considerazione degli asset

Alcuni lettori che si sono occupati di modellazione di minacce possono notare che non abbiamo parlato di asset. Abbiamo scoperto che molti tecnici software conoscono il proprio software meglio di quanto conoscano il concetto di asset e di quali asset potrebbero interessare a un utente malintenzionato.

Se si intende eseguire la modellazione delle minacce per una casa, si può iniziare pensando alla famiglia, alle foto non sostituibili o alle opere di valore. Probabilmente si inizierà a pensare a chi potrebbe introdursi e al sistema di sicurezza attuale. Oppure è possibile iniziare considerando le caratteristiche fisiche, ad esempio la piscina o il porticato anteriore. Questo equivale a pensare ad asset, utenti malintenzionati o progettazione del software. Ciascuno di questi tre approcci funziona.

L'approccio alla modellazione delle minacce che è stato presentato è sostanzialmente più semplice rispetto a ciò che Microsoft ha fatto in passato. È stato rilevato che l'approccio di progettazione del software funziona bene per molti team. Ci auguriamo che sia così anche per il lettore.

## <a name="next-steps"></a>Passaggi successivi

Inviare domande, commenti e problemi a tmtextsupport@microsoft.com. **[Scaricare](https://aka.ms/tmtpreview)** Threat Modeling Tool per iniziare.