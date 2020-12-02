---
title: Guida alla sicurezza di mini R per Azure Stack Edge | Microsoft Docs
description: Descrive le convenzioni di sicurezza, le linee guida, le considerazioni e spiega come installare e usare in modo sicuro il dispositivo Mini R Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467477"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Istruzioni per la protezione di mini R per Azure Stack Edge

![Icona di avviso 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ leggere icona ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **di avviso di sicurezza leggere informazioni sulla sicurezza e sull'integrità**

Leggere tutte le informazioni di sicurezza contenute in questo articolo prima di usare il dispositivo Mini R di Azure Stack Edge, una composizione di un pacco batteria, un alimentatore collegato AC/DC, un alimentatore modulo e un modulo server. Il mancato completamento delle istruzioni può causare incendi, scosse elettriche, lesioni o danni alle proprietà. Leggere tutte le informazioni di sicurezza riportate di seguito prima di usare Azure Stack Edge Mini R.

## <a name="safety-icon-conventions"></a>Convenzioni di sicurezza

Di seguito sono riportate le parole per i segnali di avviso di rischio:

| Icona | Descrizione |
|:--- |:--- |
| ![Simbolo di rischio](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Pericolo:** Indica una situazione pericolosa che, se non evitata, provocherà morte o lesioni gravi. <br> **Avviso:** Indica una situazione pericolosa che, se non evitata, può comportare la morte o lesioni gravi. <br> **Attenzione:** Indica una situazione pericolosa che, se non evitata, può causare lesioni minime o moderate.|
|

Quando si configura e si esegue il dispositivo Mini R Azure Stack Edge, è necessario osservare le seguenti icone di rischio:

| Icona | Descrizione |
|:--- |:--- |
| ![Leggere prima tutte le istruzioni](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Leggere prima tutte le istruzioni |
| ![Simbolo di rischio](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Simbolo di rischio |
| ![Icona di scossa elettrica](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Rischio di scosse elettriche |
| ![Solo per uso interno](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Solo per uso interno |
| ![Icona di assenza di parti riparabili dall'utente](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Nessun componente utilizzabile dall'utente. Non accedere a meno che non si sia stati adeguatamente formati. |
|

## <a name="handling-precautions-and-site-selection"></a>Gestione precauzioni e selezione sito

Il dispositivo Mini R Azure Stack Edge presenta le precauzioni di gestione seguenti e i criteri di selezione del sito:

![Icona di avviso 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ nessuna parte di servizio utilizzabile dall'utente ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Attenzione:**

* Esaminare il dispositivo *come è stato ricevuto* per individuare eventuali danni. Se lo chassis del dispositivo è danneggiato, [contattare supporto tecnico Microsoft](azure-stack-edge-placeholder.md) per ottenere una sostituzione. Non tentare di utilizzare il dispositivo.
* Se si ritiene che il dispositivo non funziona correttamente, [contattare supporto tecnico Microsoft](azure-stack-edge-placeholder.md) per ottenere una sostituzione. Non tentare di riparare il dispositivo.
* Il dispositivo non contiene parti riparabili dall'utente. All'interno sono presenti livelli pericolosi di tensione, corrente ed energia. Non aprire il dispositivo. Restituire il dispositivo a Microsoft per la manutenzione.

![Icona di avviso 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

Si consiglia di utilizzare il sistema:

* Lontano dalle fonti di calore, tra cui la luce solare diretta e i radiatori.
* In posizioni non esposte a umidità o pioggia.
* Si trova in uno spazio che riduce al minimo la vibrazione e lo shock fisico.  Il sistema è progettato per urti e vibrazioni in base a MIL-STD-810G.
* Isolamento da campi elettromagnetici forti prodotti da dispositivi elettrici.
* Non consentire l'accesso al sistema di alcun oggetto liquido o di alcun oggetto esterno. Non inserire bevande o altri contenitori Liquid nel sistema o in prossimità del sistema.

![Icona di avviso 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ nessun componente utilizzabile dall'utente ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **, attenzione:**

* Questa apparecchiatura contiene una batteria al litio. Non tentare di gestire il pacchetto batteria. Le batterie in questo dispositivo non sono utilizzabili dall'utente. Rischio di esplosione se la batteria viene sostituita da un tipo non corretto.

![Icona di avviso 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

Addebitare il pacco batteria solo quando fa parte del dispositivo Mini R Azure Stack Edge, non addebitare come dispositivo separato.

![Icona di avviso 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

* Il commutatore ON/OFF del pacco batteria è per l'esaurimento della batteria solo per il modulo server. Se la scheda di alimentazione è collegata al pacco batteria, Power viene passata al modulo server anche se il commutatore si trova nella posizione OFF.

![Icona di avviso 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

* Non masterizzare o cortocircuitare la batteria del pacchetto. Deve essere riciclata o eliminata correttamente.

![Icona di avviso 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

* Invece di usare l'alimentatore AC/DC fornito, questo sistema ha anche la possibilità di usare un campo fornito di tipo 2590 batteria. In questo caso, l'utente finale dovrà verificare che soddisfi tutti i requisiti di sicurezza, trasporto, ambiente e qualsiasi altra normativa nazionale/locale.
* Quando si utilizza il sistema con il tipo 2590 batteria, utilizzare la batteria entro le condizioni di utilizzo specificate dal produttore della batteria.

![Avviso icona 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Attenzione:**

Questo dispositivo ha due porte SFP + che possono essere usate con i ricetrasmettitori ottici. Per evitare radiazioni laser pericolose, usare solo con i ricetrasmettitori di classe 1.

## <a name="electrical-precautions"></a>Precauzioni elettriche

Il dispositivo Mini R Azure Stack Edge presenta le precauzioni elettriche seguenti:

![Icona di avviso 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Avviso:**

Se usato con l'adattatore alimentatore:

* Fornire una connessione di terra elettrica sicura per i cavi di alimentazione. Il cavo corrente alternato (AC) dispone di un plug-in di base a tre fili (un plug con un pin di base). Questa spina può essere inserita solo in una presa CA con messa a terra. Non annullare lo scopo del pin di messa a terra.
* Dato che la spina del cavo di alimentazione è il principale dispositivo di disconnessione, accertarsi che si trovino prese vicine al dispositivo e che siano facilmente accessibili.
* Scollegare i cavi di alimentazione (estraendo il connettore, non il cavo) e scollegare tutti i cavi in presenza di una delle condizioni seguenti:

  * Il cavo di alimentazione o la spina presenta sfilacciature o danni di altro tipo.
  * Il dispositivo è esposto a pioggia, a umidità in eccesso o ad altri liquidi.
  * Il dispositivo è stato eliminato e la carcassa del dispositivo è stata danneggiata.
  * Si sospetta che il dispositivo richieda un intervento di manutenzione o riparazione.
* Scollegare definitivamente l'unità prima di spostare o se si ritiene che sia stata danneggiata in alcun modo.

* Fornire una fonte di alimentazione adatta con protezione da sovraccarichi elettrici per soddisfare le specifiche tecniche seguenti:

* Tensione: 100-240 volt AC
* Corrente: 1,7 ampere
* Frequenza: da 50 a 60 Hz

![Icona di avviso 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **Avviso:**

* Non tentare di modificare o usare i cavi di alimentazione CA diversi da quelli forniti con l'apparecchiatura.

![Icona di avviso 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ interna solo ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **Avviso:**

* L'alimentatore con etichetta con questo simbolo viene valutato solo per uso interno.

## <a name="regulatory-information"></a>Informazioni sulle normative

Di seguito sono riportate informazioni normative per i dispositivi Mini R Azure Stack Edge, numero del modello normativo: TMA01.

Il dispositivo Mini R di Azure Stack Edge è progettato per l'uso con NRTL elencati (UL, CSA, ETL e così via) e IEC/EN 60950-1 o IEC/EN 62368-1 compliant (CE contrassegnato) apparecchiature it.

In paesi diversi dagli Stati Uniti e dal Canada, i cavi di rete (non forniti con l'apparecchiatura) non devono essere installati con questa apparecchiatura se la loro lunghezza è superiore a 3 metri.

L'attrezzatura è progettata per operare negli ambienti seguenti:

| Ambiente | Specifiche |
|:---  |:--- |
| Specifiche della temperatura del sistema | <ul><li>Temperatura di archiviazione: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Operazione continua: 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; F)</li></ul> |
| Specifiche di umidità relativa (RH) | <ul><li>Archiviazione: umidità relativa dal 5% al 95%</li><li>Operativo: umidità relativa dal 10% al 90%</li></ul>|
| Specifiche di altitudine massima | <ul><li>Operativo: 15.000 metri (4.572 metri)</li><li>Non operativo: 40.000 metri (12.192 metri)</li></ul>|

> ![Avviso ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** le &nbsp; modifiche o le modifiche apportate alle apparecchiature non espressamente approvate da Microsoft possono annullare l'autorità dell'utente per il funzionamento dell'apparecchiatura.

CANADA e Stati Uniti:

AVVISO: questa apparecchiatura è stata testata e risulta conforme ai limiti di una classe A un dispositivo digitale, in base alla parte 15 delle regole FCC. Questi limiti intendono fornire una protezione ragionevole da interferenze dannose quando l'attrezzatura si utilizzata in un ambiente commerciale. Questo dispositivo genera, usa e può irradiare energia a radiofrequenza e, se non installato e utilizzato in conformità con il manuale di istruzioni, potrebbe causare interferenze dannose alle comunicazioni radio. Il funzionamento di questa apparecchiatura in un'area residenziale può causare un'interferenza dannosa, nel qual caso l'utente dovrà correggere l'interferenza a proprie spese.

Questo dispositivo è conforme alla parte 15 delle normative regole FCC e agli standard RSS esenti da licenza Industry Canada. L'utilizzo è soggetto alle due condizioni seguenti: (1) il dispositivo non deve causare interferenze dannose e (2) il dispositivo deve accettare eventuali interferenze ricevute, comprese quelle che possono provocare un funzionamento indesiderato del dispositivo.

![Avviso di informazioni normative 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN CIEM-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, Stati Uniti.
Stati Uniti: (800) 426-9400 Canada: (800) 933-4750

Unione europea: richiedere una copia della dichiarazione di conformità dell'Unione europea.

> ![Icona di avviso 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) questo è un prodotto di classe. In un ambiente domestico, questo prodotto può causare interferenze radio nel qual caso l'utente può essere tenuto ad adottare misure adeguate.

Smaltimento delle batterie esauste e delle apparecchiature elettriche ed elettroniche:

![Icona di avviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Questo simbolo sul prodotto, sulle relative batterie o sull'imballaggio indica che il prodotto ed eventuali batterie incluse non devono essere smaltiti con i rifiuti domestici. È responsabilità dell'utente smaltire il prodotto in un punto di raccolta appositamente adibito al riciclaggio di batterie e apparecchiature elettriche ed elettroniche. La raccolta differenziata e il riciclaggio consentono di tutelare le risorse naturali ed evitare potenziali conseguenze negative per la salute e l'ambiente dovute alla possibile presenza di sostanze pericolose nelle batterie e nelle apparecchiature elettriche ed elettroniche che potrebbero essere rilasciate dallo smaltimento inappropriato. Per ulteriori informazioni su come smaltire le batterie e le apparecchiature elettriche ed elettroniche, contattare l'ufficio preposto del proprio comune, il servizio responsabile della raccolta dei rifiuti domestici o il negozio presso cui è stato acquistato il prodotto. Contattare erecycle@microsoft.com per ulteriori informazioni sui RAEE.

Questo prodotto contiene batterie a bottone.
Microsoft Ireland Sandyford ind est Dublin D18 KX32 IRL numero di telefono: + 353 1 295 3826 numero di fax: + 353 1 706 4110

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack mini-R Edge](azure-stack-edge-mini-r-deploy-prep.md)
