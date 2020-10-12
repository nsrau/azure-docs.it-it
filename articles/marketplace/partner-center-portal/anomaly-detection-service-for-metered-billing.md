---
title: Servizio di rilevamento anomalie per la fatturazione a consumo-Microsoft Azure Marketplace
description: Viene descritto il funzionamento del rilevamento delle anomalie, quando vengono inviate le notifiche e quali operazioni eseguire con tali funzionalità e le opzioni di supporto.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: cb134c82f231eb8b6329b1acafb181032edd6936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320086"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Servizio di rilevamento anomalie per la fatturazione a consumo

Il [servizio di misurazione del Marketplace](marketplace-metering-service-apis-faq.md) ti permette di creare offerte nel programma per Marketplace commerciale addebitate in base alle unità non standard. Con la fatturazione a consumo, si inviano eventi di utilizzo per l'utilizzo da parte del cliente a Microsoft e si prepara la fatturazione in base a tale utilizzo.

I dati di utilizzo non corretti possono provenire da numerose cause, ad esempio bug, errori di configurazione nel rilevamento del consumo o frodi. I dati di utilizzo non corretti potrebbero comportare costi non corretti per i clienti e la fatturazione.

Per attenuare il rischio, il servizio di rilevamento delle anomalie applica gli algoritmi di machine learning per determinare il normale comportamento di fatturazione a consumo, analizzare l'utilizzo della fatturazione a consumo e individuare le anomalie con un intervento minimo dell'utente.

Si riceve una notifica in caso di anomalie rilevato nell'uso della fatturazione a consumo. In questo modo è possibile analizzare e inviare una notifica se un'anomalia viene confermata come un problema reale, a quel punto è possibile intraprendere le azioni per risolvere il problema di fatturazione dei clienti in modo proattivo.

Oltre ai picchi improvvisi, ai tuffi e alle variazioni di tendenza dell'utilizzo della fatturazione a consumo, il modello rappresenta anche gli effetti stagionali. Poiché la fatturazione a consumo viene comunicata tramite dati in eccedenza, il modello è anche in grado di gestire normalmente lunghi periodi di dati mancanti.

Di seguito sono riportati alcuni esempi di risultati del rilevamento delle anomalie. L'intervallo previsto viene visualizzato come una banda gialla. L'utilizzo della fatturazione a consumo accettabile viene visualizzato come stelle verdi nella banda. L'utilizzo della fatturazione all'esterno della banda viene visualizzato come un punto rosso.  

Anomalie rilevate al di fuori di una tendenza stimabile:

![Illustra le anomalie rilevate al di fuori di una tendenza prevedibile.](media/anomaly-1.png)

Anomalie rilevate al di fuori di una tendenza ciclica ricorrente:

![Illustra le anomalie rilevate al di fuori di una tendenza ciclica ricorrente.](media/anomaly-2.png)

Anomalie rilevate in una tendenza verso l'alto:

![Illustra le anomalie rilevate in una tendenza verso l'alto.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Funzionamento del servizio rilevamento anomalie

Il rilevamento delle anomalie viene abilitato automaticamente per tutto l'utilizzo della fatturazione a consumo. Quando si inviano gli eventi di utilizzo a Microsoft, il servizio di rilevamento delle anomalie crea un modello di valori previsti in base ai dati di utilizzo precedenti. Questo modello viene eseguito settimanalmente.

Funzioni di rilevamento delle anomalie a livello di contatore e per cliente. Ciò significa che ogni misuratore con ogni cliente avrà un modello sottoposto a training in base al modello di utilizzo precedente di questo contatore del cliente.

Il modello funziona generando intervalli di confidenza retrospettiva. La previsione delle serie temporali è un modello additivo generalizzato costituito da una parte della previsione di tendenza e da una parte della stagionalità. Poiché il modello viene formulato come un'attività di regressione, può gestire normalmente lunghi periodi di dati mancanti. Se un'osservazione non rientra nell'intervallo di confidenza stimato, significa che l'osservazione non può essere spiegata in base a modelli cronologici della fatturazione a consumo e pertanto può essere un'anomalia.

## <a name="anomaly-detection-notification"></a>Notifica di rilevamento anomalie

Si inviano notifiche di rilevamento di anomalie a cadenza settimanale. Sono incluse tutte le anomalie rilevate in settimana per tutti i contatori e i clienti. Questo messaggio di posta elettronica viene inviato ai contatti di **progettazione** e **supporto** tecnico forniti al momento della creazione dell'offerta.

Si prevede di esaminare se le anomalie rilevate sono problemi reali e, in tal caso, contattare Microsoft per segnalare l'utilizzo non corretto (vedere la sezione supporto riportata di seguito).

Se si conferma che le anomalie rilevate sono di normale utilizzo, non è necessario alcun intervento da te. Tuttavia, se un'anomalia rappresenta un rischio finanziario potenzialmente elevato, è possibile che si contatti per confermare l'utilizzo.  

## <a name="when-and-how-to-get-support"></a>Quando e come ottenere supporto

Se l'utilizzo di Microsoft è stato inviato in modo errato e questo ha comportato un addebito del cliente, Microsoft non avvierà una fattura per il cliente per l'utilizzo sottoposto a segnalazioni oppure il pagamento dell'utilizzo. L'utente dovrà sostenere la perdita dei ricavi dovuta alla segnalazione di valori di utilizzo inferiori.

Se si verifica uno dei casi seguenti, è possibile aprire un ticket di supporto per richiedere il rimborso o la correzione della fatturazione per i clienti:

- Si è verificato che una delle anomalie rilevate è un problema reale e l'utilizzo errato comporta un **addebito** del cliente.
- Si scopre che è stato inviato un utilizzo errato a Microsoft e l'utilizzo errato comporta un **addebito** del cliente.
- Si desidera richiedere un rimborso per il costo dell'utilizzo della fatturazione a consumo del cliente.

Per inviare un ticket:

1. Andare alla pagina del supporto. Nella **casella informazioni sul problema**immettere "utilizzo errato".
2. In argomenti del supporto, nell'elenco a discesa dei risultati della ricerca, selezionare una delle opzioni seguenti:
    - **Marketplace commerciale**  >  **Fatturazione**  >  a consumo **Uso errato inviato per l'offerta di applicazioni Azure**
    - **Marketplace commerciale**  >  **Fatturazione**  >  a consumo **Uso errato inviato per l'offerta SaaS**
3. Nel **passaggio successivo**selezionare il pulsante **verifica soluzioni** per accedere al centro per i partner per inviare un ticket di supporto.

Per altre opzioni di supporto per gli editori, vedere [supporto per il programma Commercial Marketplace nel centro per i partner](support.md).

## <a name="next-step"></a>Passaggio successivo

- Informazioni sull' [API del servizio di misurazione del Marketplace](marketplace-metering-service-apis.md).
