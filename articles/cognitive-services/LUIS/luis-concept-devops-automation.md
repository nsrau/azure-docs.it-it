---
title: Flussi di lavoro continui per le app LUIS
description: Come implementare flussi di lavoro CI/CD per DevOps for Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 4cedf0cbe3f9ab675c191f8e4639688bb7916ee0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072995"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Flussi di lavoro di integrazione continua e recapito continuo per LUIS DevOps

I tecnici software che stanno sviluppando un'app Language Understanding (LUIS) possono applicare procedure DevOps per il [controllo del codice sorgente](luis-concept-devops-sourcecontrol.md), [compilazioni automatizzate](luis-concept-devops-automation.md), [test](luis-concept-devops-testing.md)e [Release Management](luis-concept-devops-automation.md#release-management). Questo articolo descrive i concetti relativi all'implementazione di compilazioni automatiche per LUIS.

## <a name="build-automation-workflows-for-luis"></a>Creazione di flussi di lavoro di automazione per LUIS

![Flussi di lavoro CI](./media/luis-concept-devops-automation/luis-automation.png)

Nel sistema di gestione del codice sorgente configurare pipeline di compilazione automatizzate per l'esecuzione agli eventi seguenti:

1. **Flusso di lavoro PR** attivato quando viene generata una [richiesta pull](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR). Questo flusso di lavoro convalida il contenuto della richiesta pull *prima* che gli aggiornamenti vengano Uniti nel ramo master.
1. **Flusso di lavoro ci/CD** attivato quando viene eseguito il push degli aggiornamenti nel ramo master, ad esempio quando si uniscono le modifiche da una richiesta pull. Questo flusso di lavoro garantisce la qualità di tutti gli aggiornamenti al ramo master.

Il **flusso di lavoro ci/CD** combina due processi di sviluppo complementari:

* L' [integrazione continua](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration) (ci) è la pratica di progettazione di un frequente commit del codice in un repository condiviso e l'esecuzione di una compilazione automatizzata. Abbinato a un approccio di [test](luis-concept-devops-testing.md) automatizzato, l'integrazione continua consente di verificare che per ogni aggiornamento l'origine LUDown sia ancora valida e possa essere importata in un'app Luis, ma anche che passa un gruppo di test che verificano che l'app sottoposta a training possa riconoscere gli Intent e le entità necessari per la soluzione.

* Il [recapito continuo](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery) (CD) accetta ulteriormente il concetto di integrazione continua per distribuire automaticamente l'applicazione in un ambiente in cui è possibile eseguire test più approfonditi. CD consente di apprendere tempestivamente gli eventuali problemi imprevisti che derivano dalle modifiche nel minor tempo possibile e anche per ottenere informazioni sui gap nel code coverage dei test.

L'obiettivo dell'integrazione continua e del recapito continuo è quello di garantire che "master sia sempre shippable". Per un'app LUIS, ciò significa che, se necessario, è possibile eseguire qualsiasi versione dall'app LUIS branch master e distribuirla in produzione.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Strumenti per la creazione di flussi di lavoro di automazione per LUIS

Sono disponibili diverse tecnologie di automazione della compilazione per creare flussi di lavoro di automazione della compilazione. Per tutti questi elementi è necessario che sia possibile creare script per passaggi usando un'interfaccia della riga di comando o chiamate REST in modo che possano essere eseguite in un server di compilazione.

Usare gli strumenti seguenti per la creazione di flussi di lavoro di automazione per LUIS:

* [Bot Framework Tools Luis CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) per lavorare con le app e le versioni di Luis, eseguirne il training, testarli e pubblicarli nel servizio Luis.

* [Interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) della riga di comando di Azure per eseguire query sulle sottoscrizioni di Azure, recuperare le chiavi di creazione e di stima di Luis e creare un' [entità servizio](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) di Azure usata per l'autenticazione di automazione.

* [NLU. Strumento DevOps](https://github.com/microsoft/NLU.DevOps) per [il test di un'app Luis](luis-concept-devops-testing.md) e per analizzare i risultati dei test.

### <a name="the-pr-workflow"></a>Flusso di lavoro PR

Come indicato in precedenza, questo flusso di lavoro viene configurato per l'esecuzione quando uno sviluppatore genera una richiesta pull per proporre le modifiche da unire da un ramo della funzionalità al ramo master. Lo scopo è verificare la qualità delle modifiche nella richiesta pull prima che vengano unite al ramo master.

Il flusso di lavoro deve:

* Creare un'app LUIS temporanea importando l' `.lu` origine nella richiesta pull.
* Eseguire il training e pubblicare la versione dell'app LUIS.
* Eseguire tutti gli [unit test](luis-concept-devops-testing.md) .
* Passare il flusso di lavoro se tutti i test vengono superati. in caso contrario, eseguire l'errore.
* Pulire ed eliminare l'app temporanea.

Se supportata da SCM, configurare le regole di protezione del ramo in modo che il flusso di lavoro venga completato correttamente prima di poter completare la richiesta pull.

### <a name="the-master-branch-cicd-workflow"></a>Flusso di lavoro CI/CD del ramo master

Configurare il flusso di lavoro per l'esecuzione dopo l'Unione degli aggiornamenti nella richiesta pull nel ramo master. Lo scopo è quello di garantire l'alta qualità del ramo master testando gli aggiornamenti. Se gli aggiornamenti soddisfano la barra di qualità, questo flusso di lavoro distribuisce la nuova versione dell'app LUIS in un ambiente in cui è possibile eseguire test più approfonditi.

Il flusso di lavoro deve:

* Creare una nuova versione nell'app LUIS principale, ovvero l'app che si gestisce per il ramo master, usando il codice sorgente aggiornato.

* Eseguire il training e pubblicare la versione dell'app LUIS.

  > [!NOTE]
  > Come illustrato nell' [esecuzione di test in un flusso di lavoro di compilazione automatizzato](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) , è necessario pubblicare la versione dell'app Luis sottoposta a test in modo che gli strumenti come NLU. DevOps può accedervi. LUIS supporta solo due slot di pubblicazione denominati, *gestione temporanea* e *produzione* per un'app Luis, ma è anche possibile [pubblicare una versione direttamente](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) ed [eseguire query in base alla versione](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-api-v3#changes-by-slot-name-and-version-name). Usare la pubblicazione della versione diretta nei flussi di lavoro di automazione per evitare di limitarsi a usare gli slot di pubblicazione denominati.

* Eseguire tutti gli [unit test](luis-concept-devops-testing.md).

* Eseguire facoltativamente i [test batch](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) per misurare la qualità e l'accuratezza della versione dell'app Luis e confrontarla con una linea di base.

* Se i test sono stati completati correttamente:
  * Contrassegnare l'origine nel repository.
  * Eseguire il processo di recapito continuo (CD) per distribuire la versione dell'app LUIS negli ambienti per ulteriori test.

### <a name="continuous-delivery-cd"></a>Recapito continuo (CD)

Il processo CD in un flusso di lavoro CI/CD viene eseguito in modo condizionale al completamento della compilazione e degli unit test automatizzati. Il processo consiste nel distribuire automaticamente l'applicazione LUIS in un ambiente in cui è possibile eseguire ulteriori test.

Non esiste una soluzione consigliata per il modo migliore di distribuire l'app LUIS ed è necessario implementare il processo appropriato per il progetto. Il repository di [modelli Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementa una soluzione semplice per questo, che consiste nel [pubblicare la nuova versione dell'app Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) nello slot di pubblicazione di *produzione* . Questa operazione è corretta per una semplice installazione. Tuttavia, se è necessario supportare contemporaneamente diversi ambienti di produzione, ad esempio *sviluppo*, *gestione temporanea* e *UAT*, il limite di due slot di pubblicazione denominati per ogni applicazione risulterà insufficiente.

Altre opzioni per la distribuzione di una versione dell'app includono:

* Lasciare la versione dell'app pubblicata nell'endpoint della versione diretta e implementare un processo per configurare gli ambienti di produzione downstream con l'endpoint della versione diretta come richiesto.
* Mantenere diverse app LUIS per ogni ambiente di produzione e scrivere passaggi di automazione per importare `.lu` in una nuova versione dell'app Luis per l'ambiente di produzione di destinazione, per eseguire il training e la pubblicazione.
* Esportare la versione testata dell'app LUIS in un [contenitore Luis Docker](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto?tabs=v3) e distribuire il contenitore Luis in [istanze di contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure.

## <a name="release-management"></a>Gestione versioni

In genere è consigliabile eseguire il recapito continuo solo negli ambienti non di produzione, ad esempio per lo sviluppo e la gestione temporanea. La maggior parte dei team richiede un processo di revisione e approvazione manuale per la distribuzione in un ambiente di produzione. Per una distribuzione di produzione, è consigliabile assicurarsi che si verifichi quando le persone chiave del team di sviluppo sono disponibili per il supporto o durante i periodi di traffico ridotto.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [implementare DevOps per Luis con GitHub](luis-how-to-devops-with-github.md)
* Informazioni su come scrivere un [flusso di lavoro di azioni GitHub con NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
