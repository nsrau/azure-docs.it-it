---
title: File di inclusione
description: File di inclusione
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 41418cb908f2bf149a3d0087728652b44cd6b19e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825532"
---
Finora è stato eseguito il codice dell'applicazione come se si fosse l'unico sviluppatore a lavorare sull'app. In questa sezione, si apprenderà come Azure Dev Spaces semplifichi lo sviluppo in team:
* Consente a un team di sviluppatori di lavorare nello stesso ambiente, usando uno spazio di sviluppo condiviso o spazi di sviluppo distinti in base alle esigenze.
* Supporta ogni sviluppatore che itera sul proprio codice in isolamento senza il timore di interrompere gli altri.
* Permette di testare il codice end-to-end, prima di eseguirne il commit, senza dover creare simulazioni delle dipendenze.

### <a name="challenges-with-developing-microservices"></a>Problemi con lo sviluppo di microservizi
L'applicazione di esempio non è molto complessa al momento. Nello sviluppo nel mondo reale, tuttavia, le sfide emergono appena si aggiungono più servizi e il team di sviluppo cresce.

Si immagini di lavorare a un servizio che interagisce con dozzine di altri servizi.

- Può diventare irrealistico eseguire tutto in locale per lo sviluppo. Il computer di sviluppo potrebbe non disporre di risorse sufficienti per eseguire l'intera app. In alternativa, è probabile che l'app disponga di endpoint che devono essere raggiungibili pubblicamente (ad esempio l'app risponde a un webhook da un'app SaaS).

- È possibile provare a eseguire solo i servizi da cui si dipende, ma in tal caso sarebbe necessario conoscere la chiusura completa delle dipendenze (ad esempio le dipendenze delle dipendenze). Potrebbe anche non essere semplice capire come compilare ed eseguire le proprie dipendenze perché non si è lavorato su di esse.
- Alcuni sviluppatori ricorrono alla simulazione di molte delle loro dipendenze di servizio. Questo approccio può essere utile a volte, ma la gestione di quelle simulazioni può pesare presto sul proprio lavoro di sviluppo. Inoltre, con questo approccio lo spazio di sviluppo è visto in modo molto diverso dalla produzione e possono insorgere bug.
- È quindi difficile eseguire qualsiasi tipo di test end-to-end. I test di integrazione possono avvenire in modo realistico solo dopo il commit, il che significa che i problemi verranno riscontrati più avanti nel ciclo di sviluppo.

![](../media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>Lavorare in uno spazio di sviluppo condiviso
Con Azure Dev Spaces è possibile configurare uno spazio di sviluppo *condiviso* in Azure. Ogni sviluppatore può concentrarsi solo sulla propria parte dell'applicazione e può sviluppare in modo iterativo *codice pre-commit* in uno spazio di sviluppo che contiene già tutti gli altri servizi e le risorse cloud da cui dipendono i propri scenari. Le dipendenze sono sempre aggiornate e gli sviluppatori lavorano in un modo che rispecchia la produzione.

### <a name="work-in-your-own-space"></a>Lavorare nello spazio personale
Quando si sviluppa codice per un servizio, prima che sia pronto per l'archiviazione, il codice spesso non è in buono stato. Sono ancora in corso attività iterative di modellazione, test e sperimentazione con le soluzioni. Azure Dev Spaces fornisce il concetto di uno **spazio**, che consente di lavorare in isolamento senza timore di interrompere i membri del team.

> [!Note]
> Prima di procedere, chiudere tutte le finestre di Visual Studio Code per entrambi i servizi, quindi eseguire `azds up -d` in ciascuna delle cartelle radice del servizio. (Si tratta di una limitazione di anteprima).

Diamo uno sguardo più da vicino a dove i servizi sono attualmente in esecuzione. Con il comando `azds list` si otterrà un output simile al seguente:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     http://webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

La colonna Space (Spazio) mostra che entrambi i servizi sono in esecuzione in uno spazio denominato `default`. Chiunque apra l'URL pubblico e navighi verso l'app Web invocherà il percorso del codice precedentemente scritto che viene eseguito attraverso entrambi i servizi. Supponiamo ora di voler continuare a sviluppare `mywebapi`. Come si possono apportare modifiche al codice e testarle e non interrompere gli altri sviluppatori che usano l'ambiente di sviluppo? A tale scopo, è necessario impostare il proprio spazio.

### <a name="create-a-space"></a>Creare uno spazio
Per eseguire la propria versione di `mywebapi` in uno spazio diverso da `default` è possibile creare il proprio spazio usando il comando seguente:

``` 
azds space create --name scott
```

Nell'esempio precedente per il nuovo spazio è stato usato il nome dell'utente, in modo che i colleghi possano identificarlo, che rappresenta lo spazio in cui l'utente sta lavorando, ma è possibile chiamarlo come si preferisce e inserire il significato desiderato, come "sprint4" o "demo".

Eseguire il comando `azds space list` per visualizzare un elenco di tutti gli spazi presenti nell'ambiente di sviluppo. Viene visualizzato un asterisco (*) accanto allo spazio attualmente selezionato. Nel caso specifico, lo spazio denominato "scott" è stato selezionato automaticamente al momento della creazione. È possibile selezionare un altro spazio in qualsiasi momento con il comando `azds space select`.
