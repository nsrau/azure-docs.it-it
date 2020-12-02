---
title: Guida alla protezione di Azure Stack Edge Pro R | Microsoft Docs
description: Descrive le convenzioni di sicurezza, le linee guida, le considerazioni e spiega come installare e usare in modo sicuro il dispositivo R Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: ac3f2cc1c68ea552b2858d932217a28055fee0fd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467576"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Istruzioni di sicurezza R per Pro Azure Stack Edge

![Icona di avviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![Icona di avviso di sicurezza lettura](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**LETTURA INFORMAZIONI DI SICUREZZA E INTEGRITÀ**

Prima di usare il dispositivo R Azure Stack Edge Pro, leggere tutte le informazioni di sicurezza contenute in questo articolo. Il mancato completamento delle istruzioni può causare incendi, scosse elettriche, lesioni o danni alle proprietà. Leggere tutte le informazioni di sicurezza riportate di seguito prima di usare Azure Stack Edge Pro R.

## <a name="safety-icon-conventions"></a>Convenzioni di sicurezza

Di seguito sono riportate le parole per i segnali di avviso di rischio:

| Icona | Descrizione |
|:--- |:--- |
| ![Simbolo di rischio](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **Pericolo:** Indica una situazione pericolosa che, se non evitata, provocherà morte o lesioni gravi. <br> **Avviso:** Indica una situazione pericolosa che, se non evitata, può comportare la morte o lesioni gravi. <br> **Attenzione:** Indica una situazione pericolosa che, se non evitata, può causare lesioni minime o moderate.|
|

Quando si configura e si esegue il dispositivo R Edge Pro Azure Stack Edge, è necessario osservare le seguenti icone di rischio:

| Icona | Descrizione |
|:--- |:--- |
| ![Leggere prima tutte le istruzioni](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Leggere prima tutte le istruzioni |
| ![Simbolo di rischio](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Simbolo di rischio |
| ![Icona di suggerimento di pericolo](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Rischio di ribaltamento|
| ![Icona di carico pesante](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Rischio elevato|
| ![Icona di scossa elettrica](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Rischio di scosse elettriche |
| ![Icona di assenza di parti riparabili dall'utente](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Nessun componente utilizzabile dall'utente. Non accedere a meno che non si sia stati adeguatamente formati. |
| ![Icona di più fonti di alimentazione](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Più fonti di alimentazione. Scollegare tutti i cavi di alimentazione per rimuovere tutta la potenza dall'apparecchiatura. |
| ![Icona punti di pizzico](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Sono presenti punti di pizzicotti. |
| ![Icona componenti o superfici sensibili](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Indica componenti o superfici sensibili. |
|

## <a name="handling-precautions-and-site-selection"></a>Gestione precauzioni e selezione sito

![Icona di avviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **AVVISO:**

* È necessario usare dispositivi appropriati, ad esempio pallet Jack, e Personal protettiv Equipment (dpi), ad esempio, quando si esegue lo stato di trasferimento e gestione del dispositivo come fornito.

![Icona di avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ Suggerimento rischio icona ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **Avviso:**

* Posizionare l'apparecchiatura su una superficie piana, rigida e stabile per evitare potenziali suggerimenti o rischi di schiacciamento.
* Non eseguire lo stack di più di 2 dispositivi tra loro.
* Prima di eseguire lo stack, verificare che il sistema sia protetto.
* Non rilocare o spostare i dispositivi in pila.
* Non eseguire lo stack dei dispositivi che vengono eccitati con cavi esterni.
* Assicurarsi che i cavi di alimentazione non siano schiacciati o danneggiati durante le operazioni di sovrapposizione.
* I dispositivi non devono essere usati come tabelle o aree di lavoro.

![Icona di avviso icona di ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ scossa elettrica ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ Nessuna icona delle parti utilizzabile dall'utente ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **Attenzione:**

* Esaminare il dispositivo *come è stato ricevuto* per individuare eventuali danni. Se lo chassis del dispositivo è danneggiato, [contattare supporto tecnico Microsoft](azure-stack-edge-contact-microsoft-support.md) per ottenere una sostituzione. Non tentare di utilizzare il dispositivo.
* Se si ritiene che il dispositivo non funziona correttamente, [contattare supporto tecnico Microsoft](azure-stack-edge-contact-microsoft-support.md) per ottenere una sostituzione. Non tentare di riparare il dispositivo.
* Il dispositivo non contiene parti riparabili dall'utente. All'interno sono presenti livelli pericolosi di tensione, corrente ed energia. Non aprire il dispositivo. Restituire il dispositivo a Microsoft per la manutenzione.

![Icona di avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ icona peso elevato ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **Avviso:**

* Rimuovendo il modulo di alimentazione del gruppo di continuità, le parti attive vengono esposte in UPS. Non inserire oggetti esterni all'interno del raggruppamento del modulo di alimentazione.
* Non provare a sollevare autonomamente un dispositivo R Edge Pro Azure Stack Edge. Un'enclosure può pesare tra 52 kg e 93 kg (115 lbs e 205 lbs); usare l'assistenza tecnica o un'altra assistenza adatta per lo strumento di trasferimento e sollevamento. Conformità ai requisiti di sicurezza e integrità occupazionali locali quando si trasferiscono e si sollevano apparecchiature.
* Non tentare di sollevare l'attrezzatura senza un supporto meccanico appropriato. Tenere presente che il tentativo di sollevare questo peso può causare gravi danni.

![Icona di avviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)  **AVVISO:**

* Il sistema è progettato per funzionare in un ambiente controllato. Scegliere un sito:
  * Ben ventilato e lontano dalle fonti di calore, tra cui la luce solare diretta e i radiatori.
  * Non esposto ad umidità o pioggia.
  * Si trova in uno spazio che riduce al minimo la vibrazione e lo shock fisico.  Il sistema è progettato per urti e vibrazioni in base a MIL-STD-810G.
  * Isolamento da campi elettromagnetici forti prodotti da dispositivi elettrici.
  * Fornito con Outlet con basi corrette.
  * Fornito con spazio sufficiente per accedere ai cavi di alimentazione, perché funge da disconnessione elettrica principale del prodotto.
* Configurare le apparecchiature in un'area di lavoro, in modo da consentire una circolazione aerea adeguata intorno alle apparecchiature; Assicurarsi che le coperture anteriore e posteriore siano rimosse completamente mentre il dispositivo è in esecuzione.
* Installare le apparecchiature in un'area controllata a temperatura senza contaminazioni conduttive e consentire una circolazione aerea adeguata intorno alle apparecchiature.
* Evitare l'uso di apparecchiature da origini di ambienti liquidi e eccessivamente umidi.
* Non consentire l'accesso al sistema di alcun oggetto liquido o di alcun oggetto esterno. Non inserire bevande o altri contenitori Liquid nel sistema o in prossimità del sistema.

## <a name="heater-precautions"></a>Precauzioni di riscaldamento

![Icona di avviso icona dei ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ componenti o delle aree sensibili 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Avviso:** 

* L'operazione di riscaldamento automatico mentre il sistema è accesa può creare un rischio di tocco a causa di temperature di superficie elevata sulla copertura degli assembly dei riscaldatori. Non toccare questa superficie mentre il sistema è acceso. Consentire un periodo di raffreddamento di 10 minuti dopo che il sistema è stato spento.

![Icona di avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ icona punti di pizzicamento 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Avviso:** 

* Quando il sistema è acceso, l'attivazione automatica dello sportello di plenum posteriore può creare un rischio per puntini di arresto. Quando il sistema è acceso, è opportuno chiarire questo aspetto.

## <a name="electrical-precautions"></a>Precauzioni elettriche

![Icona di avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ icona di scossa elettrica ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Avviso:**

* Fornire una connessione di terra elettrica sicura per i cavi di alimentazione. Il cavo corrente alternato (AC) dispone di un plug-in di base a tre fili (un plug con un pin di base). Questa spina può essere inserita solo in una presa CA con messa a terra. Non annullare lo scopo del pin di messa a terra.
* Dato che la spina del cavo di alimentazione è il principale dispositivo di disconnessione, accertarsi che si trovino prese vicine al dispositivo e che siano facilmente accessibili.
* Scollegare i cavi di alimentazione (estraendo il connettore, non il cavo) e scollegare tutti i cavi in presenza di una delle condizioni seguenti:

  * Il cavo di alimentazione o la spina presenta sfilacciature o danni di altro tipo.
  * Si versa qualcosa nell'involucro del dispositivo.
  * Il dispositivo viene esposto a pioggia o umidità eccessiva.
  * Il dispositivo è caduto e l'involucro si è danneggiato.
  * Si sospetta che il dispositivo richieda un intervento di manutenzione o riparazione.
* Scollegare definitivamente l'unità prima di spostare o se si ritiene che sia stata danneggiata in alcun modo.
* Fornire una fonte di alimentazione adatta con protezione da sovraccarichi elettrici per soddisfare le specifiche tecniche seguenti:

  * Tensione: da 100 a 240 volt AC
  * Corrente: 20 A, massimo per cavo di alimentazione. Vengono forniti i cavi di alimentazione.
  * Frequenza: da 50 a 60 Hz

![Icona di avviso icona di ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ scossa elettrica ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ più fonti di alimentazione ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **Avviso:**

* Per i sistemi senza interruzioni di alimentazione (UPS), scollegare tutti i cavi di alimentazione CA per rimuovere completamente l'alimentazione CA dalle apparecchiature.
* Per i sistemi con UPS, scollegare tutti i cavi di alimentazione CA e usare l'interruttore di alimentazione UPS per disattivare il sistema. UPS contiene tensioni pericolose AC e DC.

![Icona di avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Avviso:**

* Per i sistemi dotati di UPS, la tensione AC e/o DC comporterà sempre un potenziale rischio di tensione AC nell'output di UPS generato da batterie o utilità. Per evitare danni alle apparecchiature o lesioni personali, si presuppone sempre che vi sia una tensione nell'output del gruppo di continuità, anche in caso di disconnessione dalla fonte di alimentazione primaria.
* Per i sistemi dotati di UPS, tutti gli UPS con le connessioni esterne eccitate sono femminili. Non rimuovere il case o inserire niente in questi o tutti i connettori nei UPS.

![Icona di avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ icona di scossa elettrica ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **Avviso:**

* Non tentare di modificare o usare i cavi di alimentazione CA diversi da quelli forniti con l'apparecchiatura.

![Icona di avviso senza l'icona delle parti utilizzabili ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ dall'utente ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **Attenzione:**

* Questa apparecchiatura contiene batterie di litio e/o fosfato di litio ferro. Non tentare di gestire l'apparecchiatura. Le batterie in questo dispositivo non sono utilizzabili dall'utente. Rischio di esplosione se la batteria viene sostituita da un tipo non corretto.
* La rimozione del modulo batteria del gruppo di continuità espone le parti energizzate all'interno del gruppo di continuità. Non inserire oggetti esterni all'interno del raggruppamento del modulo della batteria.

## <a name="regulatory-information"></a>Informazioni sulle normative

Questa sezione contiene informazioni sulle normative per Azure Stack dispositivo R Edge Pro, numero del modello normativo: Azure Stack Edge Pro R.

Il dispositivo R Edge Pro Azure Stack Edge è stato progettato per l'uso con NRTL elencati (UL, CSA, ETL e così via) e IEC/EN 60950-1 o IEC/EN 62368-1 compliant (CE contrassegnato) apparecchiature it.

Il dispositivo è progettato per operare negli ambienti seguenti:

| Ambiente | Specifiche |
|:--- |:--- |
|Specifiche di temperatura | <ul><li>Temperatura di archiviazione: – 33 &deg; c – 63 &deg; c (– 28 &deg; f-145 &deg; f) </li><li>Operazione continua: 5 &deg; c – 43 &deg; c (41 &deg; f – 110 &deg; f)</li><li>Gradiente temperatura massima (operativo e archiviazione): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Specifiche relative all'umidità | <ul><li>Archiviazione: da 5% a 95% RH con 33 &deg; C (91 &deg; F) punto di rugiada massimo. L'ambiente deve essere sempre non condensato.</li><li>Funzionamento: umidità relativa dal 5% al 85% con 29 &deg; C (84,2 &deg; F) punto di rugiada massimo</li></ul> |
| Specifiche di altitudine massima | <ul><li>Operativo (senza UPS): 15.000 ft (4.572 metri)</li><li>Operativo (con UPS): 6,561 ft (2.000 metri)</li><li>Archiviazione: 40.000 ft (12.192 metri)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Avviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **:** le &nbsp; modifiche o le modifiche apportate alle apparecchiature non espressamente approvate da Microsoft possono annullare l'autorità dell'utente per il funzionamento dell'apparecchiatura.

CANADA e Stati Uniti:

AVVISO: questa apparecchiatura è stata testata e risulta conforme ai limiti di una classe A un dispositivo digitale, in base alla parte 15 delle regole FCC. Questi limiti intendono fornire una protezione ragionevole da interferenze dannose quando l'attrezzatura si utilizzata in un ambiente commerciale. Questo dispositivo genera, usa e può irradiare energia a radiofrequenza e, se non installato e utilizzato in conformità con il manuale di istruzioni, potrebbe causare interferenze dannose alle comunicazioni radio. Il funzionamento di questa apparecchiatura in un'area residenziale può causare un'interferenza dannosa, nel qual caso l'utente dovrà correggere l'interferenza a proprie spese.

Questo dispositivo è conforme alla parte 15 delle normative regole FCC e agli standard RSS esenti da licenza Industry Canada. L'utilizzo è soggetto alle due condizioni seguenti: (1) il dispositivo non deve causare interferenze dannose e (2) il dispositivo deve accettare eventuali interferenze ricevute, comprese quelle che possono provocare un funzionamento indesiderato del dispositivo.

![Avviso di informazioni normative 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN CIEM-3 (A)/NMB-3 (A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, Stati Uniti.
Stati Uniti: (800) 426-9400 Canada: (800) 933-4750

Unione europea: richiedere una copia della dichiarazione di conformità dell'Unione europea. 

![Icona di avviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)

Si tratta di un prodotto di Classe A. In un ambiente domestico, questo prodotto può causare interferenze radio nel qual caso l'utente può essere tenuto ad adottare misure adeguate.

Smaltimento delle batterie esauste e delle apparecchiature elettriche ed elettroniche:

![Icona di avviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Questo simbolo sul prodotto, sulle relative batterie o sull'imballaggio indica che il prodotto ed eventuali batterie incluse non devono essere smaltiti con i rifiuti domestici. È responsabilità dell'utente smaltire il prodotto in un punto di raccolta appositamente adibito al riciclaggio di batterie e apparecchiature elettriche ed elettroniche. La raccolta differenziata e il riciclaggio consentono di tutelare le risorse naturali ed evitare potenziali conseguenze negative per la salute e l'ambiente dovute alla possibile presenza di sostanze pericolose nelle batterie e nelle apparecchiature elettriche ed elettroniche che potrebbero essere rilasciate dallo smaltimento inappropriato. Per ulteriori informazioni su come smaltire le batterie e le apparecchiature elettriche ed elettroniche, contattare l'ufficio preposto del proprio comune, il servizio responsabile della raccolta dei rifiuti domestici o il negozio presso cui è stato acquistato il prodotto. Contattare erecycle@microsoft.com per ulteriori informazioni sui RAEE.

Questo prodotto contiene batterie a bottone.

Microsoft Ireland Sandyford ind est Dublin D18 KX32 IRL numero di telefono: + 353 1 295 3826 numero di fax: + 353 1 706 4110



## <a name="next-steps"></a>Passaggi successivi

- [Prepararsi alla distribuzione di Azure Stack Edge Pro Edge](azure-stack-edge-pro-r-deploy-prep.md)
