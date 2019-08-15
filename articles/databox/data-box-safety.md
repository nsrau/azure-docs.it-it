---
title: Sicurezza per Azure Data Box | Microsoft Docs
description: Vengono descritte considerazioni, linee guida e convenzioni di sicurezza e viene spiegato come installare e utilizzare Azure Data Box in modo sicuro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: db6e2eadbf19d78a203a4d4ba6111ad88430b192
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985937"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Installazione sicura e funzionamento di Azure Data Box
![Icona di avviso](./media/data-box-safety/warning_icon.png)
![Icona di avviso di sicurezza lettura](./media/data-box-safety/read_safety_and_health_information_icon.png) **LETTURA INFORMAZIONI DI SICUREZZA E INTEGRITÀ**

Leggere tutte le informazioni di sicurezza in questo articolo prima di usare Azure Data Box. La mancata osservanza delle istruzioni potrebbe causare incendi, scosse elettriche o altre lesioni o danni alle proprietà.

## <a name="safety-icon-conventions"></a>Convenzioni di sicurezza
Ecco le icone che vengono visualizzate quando si esaminano le precauzioni di sicurezza cui attenersi durante l'installazione e l'utilizzo di Data Box.

| Icona | DESCRIZIONE |
|:--- |:--- |
| ![Icona di pericolo](./media/data-box-safety/warning_icon.png) **PERICOLO!** |Indica una situazione di pericolo che, se non viene evitato, comporterà morte o gravi ferite. Questa parola deve essere limitata a situazioni più estreme. |
| ![Icona di avviso](./media/data-box-safety/warning_icon.png)**AVVISO!** |Indica una situazione di pericolo che, se non viene evitata, può comportare morte o gravi ferite. |
| ![Icona di avviso](./media/data-box-safety/warning_icon.png)**ATTENZIONE:** |Indica una situazione di pericolo che, se non viene evitato, comporterà ferite lievi o limitate. |
| ![Icona di notifica](./media/data-box-safety/notice_icon.png)**NOTIFICA:** |Indica le informazioni considerate importanti, ma non correlate al rischio. |
| ![Icona di scossa elettrica](./media/data-box-safety/electrical_shock_hazard_icon.png)**pericolo di scosse elettriche** |Alta tensione. |
| ![Icona peso elevato](./media/data-box-safety/heavy_weight_hazard_icon.png) **Pesante** | |
| ![Nessuna icona di parti riparabili dall'utente](./media/data-box-safety/no_user_serviceable_parts_icon.png) **Nessuna parte riparabile dall'utente** |Non accedere a meno che non si sia stati adeguatamente formati. |
| ![Icona di avviso di sicurezza lettura](./media/data-box-safety/read_safety_and_health_information_icon.png) **Leggere prima tutte le istruzioni** | |
| ![Suggerimento di pericolo icona](./media/data-box-safety/tip_hazard_icon.png)**Suggerimento di pericolo** | |

## <a name="handling-precautions"></a>Precauzioni relative alla gestione

![Icona di avviso](./media/data-box-safety/warning_icon.png) ![Icona di scossa elettrica](./media/data-box-safety/electrical_shock_hazard_icon.png)![Icona di parti non riparabili dall'utente](./media/data-box-safety/no_user_serviceable_parts_icon.png) **ATTENZIONE** 

* Esaminare il dispositivo *come è stato ricevuto* per individuare eventuali danni. Se lo chassis del dispositivo è danneggiato, [contattare supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md) per ottenere una sostituzione. Non tentare di utilizzare il dispositivo. 
* Il dispositivo è dotato di viti a prova di manomissione. Se si ritiene che il dispositivo non funziona correttamente, [contattare supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md) per ottenere una sostituzione. Non tentare di riparare il dispositivo. 
* Il dispositivo non contiene parti riparabili dall'utente. All'interno sono presenti livelli pericolosi di tensione, corrente ed energia. Non aprire il dispositivo. Restituire il dispositivo a Microsoft per la manutenzione.

![Icona di avviso](./media/data-box-safety/warning_icon.png) ![Icona peso elevato](./media/data-box-safety/heavy_weight_hazard_icon.png) **AVVISO!** 

* Un'enclosure completamente configurata può pesare fino a 22,7 kg (50 libbre); non tentare di sollevarla da soli.
* Prima di spostare lo chassis, assicurarsi sempre che due utenti siano disponibili per gestire il peso. Tenere presente che una persona tentando di sollevare questo peso è in grado di sostenere lesioni.


![Icona di avviso](./media/data-box-safety/warning_icon.png) ![Suggerimento di pericolo icona](./media/data-box-safety/tip_hazard_icon.png) **AVVISO!**
* Collocare il dispositivo su una superficie piana, rigida e stabile per evitare potenziali rischi di cadute.
* Le apparecchiature montate su rack non devono essere utilizzate come scaffali o aree di lavoro. Non posizionare Data Box sopra apparecchiature montate su rack. L'aggiunta di qualsiasi tipo di carico a un'unità montata su rack protesa può causare potenziali rischi di cadute con conseguente pericolo di lesioni, morte o danni al prodotto.

![Icona di avviso](./media/data-box-safety/warning_icon.png)**AVVISO!**

* Sistemare il dispositivo in un'area di lavoro con adeguata circolazione dell'aria intorno al dispositivo.
* Installare il dispositivo in un locale a temperatura controllata, priva di contaminanti conduttivi e con un'adeguata circolazione dell'aria intorno al dispositivo.
* Mantenere il dispositivo lontano da fonti di liquidi e ambienti eccessivamente umidi.


## <a name="electrical-precautions"></a>Precauzioni elettriche

![Warning Icon](./media/data-box-safety/warning_icon.png)![Electrical Shock Icon](./media/data-box-safety/electrical_shock_hazard_icon.png)**AVVISO!**

* Fornire una connessione di terra elettrica sicura per i cavi di alimentazione. Il cavo CA ha una spina a tre fili con messa a terra (spina con pin di messa a terra). Questa spina può essere inserita solo in una presa CA con messa a terra. Non annullare lo scopo del pin di messa a terra.
* Dato che la spina del cavo di alimentazione è il principale dispositivo di disconnessione, accertarsi che si trovino prese vicine al dispositivo e che siano facilmente accessibili.
* Scollegare il cavo di alimentazione (tirando la spina, non il cavo) e disconnettere tutti gli eventuali cavi in presenza di una delle condizioni seguenti:

    - Il cavo di alimentazione o la spina presenta sfilacciature o danni di altro tipo.
    - Si versa qualcosa nell'involucro del dispositivo.
    - Il dispositivo viene esposto a pioggia o umidità eccessiva.
    - Il dispositivo è caduto e l'involucro si è danneggiato.
    - Si sospetta che il dispositivo richieda un intervento di manutenzione o riparazione.
* Scollegare definitivamente l'unità prima di spostare o se si ritiene che sia stata danneggiata in alcun modo.
* Fornire una fonte di alimentazione adatta con protezione da sovraccarichi elettrici per soddisfare le specifiche tecniche seguenti:

    - Tensione da 100 v AC a 240 V AC
    - Corrente 6 A, massimo
    - Frequenza: da 50 Hz a 60 Hz

![Icona di avviso](./media/data-box-safety/warning_icon.png) **ATTENZIONE:**

* Questo dispositivo contiene batterie a bottone. Non tentare di riparare il dispositivo. Le batterie di questo dispositivo non sono riparabili dall'utente. 
* **Solo per il personale del servizio**: Rischio di esplosione se la batteria viene sostituita da un tipo non corretto. Smaltire le batterie esauste in base alle istruzioni.

![Icona di notifica](./media/data-box-safety/notice_icon.png)**NOTIFICA:**

Per il corretto funzionamento dell'apparecchiatura e per evitare danni al dispositivo:

* Assicurarsi che gli sportelli anteriore e posteriore siano completamente aperti durante l'uso del dispositivo.

## <a name="regulatory-information"></a>Informazioni sulle normative

Questa sezione contiene informazioni normative per Azure Data Box, numero di modello normativo DB010.

Questo dispositivo è:

- Valutato come apparecchiatura IT (Information Technology Equipment, ITE), progettata per l'uso in un tipico ambiente di data room. L'idoneità di questo prodotto in altri ambienti può richiedere un'ulteriore valutazione.
- Progettato per l'uso con apparecchiature IT riconosciute NRTL (UL, CSA, ETL e così via) e compatibili con IEC/EN 60950-1 o IEC/EN 62368-1 (contrassegno CE).
- Progettato per funzionare nell'ambiente seguente. 
    - Temperatura operativa: da 50 ° a 95 ° F (da 10 ° a 35 ° C)
    - Temperatura di magazzino: da -20° a 50° C (da -4° a 122° F)
    - Umidità relativa: dal 15% al 85% (non in condensa) 
    - Altitudine operativa: Testato fino a 6500 metri (0 metri a 2000 metri)

Per il valore nominale, fare riferimento all'etichetta del dispositivo fornita con l'unità. 

![Icona di notifica](./media/data-box-safety/notice_icon.png)**NOTIFICA:** 

Qualsiasi modifica apportata al dispositivo non espressamente approvata da Microsoft può invalidare l'autorità dell'utente all'uso del dispositivo.

**CANADA e Stati Uniti:**

![Icona di notifica](./media/data-box-safety/notice_icon.png)**NOTIFICA:** 

Questo dispositivo è stato testato e valutato come conforme ai limiti di un dispositivo digitale di Classe A, ai sensi della parte 15 delle normative FCC. Questi limiti intendono fornire una protezione ragionevole da interferenze dannose quando l'attrezzatura si utilizzata in un ambiente commerciale. Questo dispositivo genera, usa e può irradiare energia a radiofrequenza e, se non installato e utilizzato in conformità con il manuale di istruzioni, potrebbe causare interferenze dannose alle comunicazioni radio. Il funzionamento di questa apparecchiatura in un'area residenziale può causare un'interferenza dannosa, nel qual caso l'utente dovrà correggere l'interferenza a proprie spese.

Questo dispositivo è conforme alla parte 15 delle normative regole FCC e agli standard RSS esenti da licenza Industry Canada. L'operazione è soggetta alle due condizioni seguenti: (1) questo dispositivo potrebbe non causare un'interferenza dannosa e (2) il dispositivo deve accettare qualsiasi interferenza ricevuta, incluse le interferenze che possono causare un funzionamento indesiderato del dispositivo.

![Canada](./media/data-box-safety/canada.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.

Stati Uniti: (800) 426-9400

Canada: (800) 933-4750

**UNIONE EUROPEA:**

[Richiedere una copia della dichiarazione di conformità dell'Unione europea](mailto:CSI_Compliance@microsoft.com).

![Icona di avviso](./media/data-box-safety/warning_icon.png) **AVVISO:** 

Si tratta di un prodotto di Classe A. In un ambiente domestico, questo prodotto può causare interferenze radio nel qual caso l'utente può essere tenuto ad adottare misure adeguate.

**Smaltimento delle batterie esauste e delle apparecchiature elettriche ed elettroniche:**

![Icona di smaltimento della batteria](./media/data-box-safety/battery_disposal_icon.png)

Questo simbolo sul prodotto, sulle relative batterie o sull'imballaggio indica che il prodotto ed eventuali batterie incluse non devono essere smaltiti con i rifiuti domestici. È responsabilità dell'utente smaltire il prodotto in un punto di raccolta appositamente adibito al riciclaggio di batterie e apparecchiature elettriche ed elettroniche. La raccolta differenziata e il riciclaggio consentono di tutelare le risorse naturali ed evitare potenziali conseguenze negative per la salute e l'ambiente dovute alla possibile presenza di sostanze pericolose nelle batterie e nelle apparecchiature elettriche ed elettroniche che potrebbero essere rilasciate dallo smaltimento inappropriato. Per ulteriori informazioni su come smaltire le batterie e le apparecchiature elettriche ed elettroniche, contattare l'ufficio preposto del proprio comune, il servizio responsabile della raccolta dei rifiuti domestici o il negozio presso cui è stato acquistato il prodotto. Per ulteriori informazioni su RAEE, contattare *erecycle\@Microsoft.com* .

Questo prodotto contiene batterie a bottone.

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Numero di telefono: +353 1 295 3826

Numero di fax: +353 1 706 4110 

**Giappone**

![Giappone](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Dopo aver esaminato questi avvisi sulla sicurezza, è possibile installare e collegare il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

* [Inserire il cavo e connettere Data Box](data-box-deploy-set-up.md)


