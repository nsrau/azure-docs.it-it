<properties
   pageTitle="Guida per più aree geografiche | Microsoft Azure"
   description="Informazioni su come creare un'area di lavoro e pubblicare un servizio web in un'area di Azure diversa da quella degli Stati Uniti centro meridionali (SCUS)."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>  

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# Guida per più aree geografiche

In questo articolo viene descritto come creare un'area di lavoro e pubblicare un servizio web in altre aree geografiche di Azure.

## Creare un'area di lavoro

1. Accedere al portale di Microsoft Azure classico.

2.  Fare clic su **+ NUOVO** > **SERVIZI DATI** > **MACHINE LEARNING** > **CREAZIONE RAPIDA**. In **INDIRIZZO** selezionare un'altra area, ad esempio **Asia sudorientale**. ![Guida per più aree geografiche immagine 1][1]
3. Selezionare l'area di lavoro, quindi fare clic su **Accedi a ML Studio**. ![Guida per più aree geografiche immagine 2][2]

4. Ora si dispone di un'area di lavoro in un'altra area geografica che può essere utilizzata come qualsiasi altra area di lavoro. Per passare da un’area di lavoro ad un’altra, cercare in alto a destra dello schermo. Fare clic sul menu a discesa, selezionare l'area geografica e quindi selezionare l'area di lavoro. Tutto è locale all'area geografica dell’area di lavoro; ad esempio, tutti i servizi web creati da un'area di lavoro saranno nella stessa area geografica in cui si trova l'area di lavoro. ![Guida per più aree geografiche immagine 3][3]

## Aprire un esperimento dalla raccolta

Se si apre un esperimento dalla raccolta, è inoltre possibile selezionare in quale area geografica si desidera copiare l’esperimento.

![Guida per più aree geografiche immagine 4][4a]

## Gestione del servizio Web

Per gestire a livello di programmazione i servizi web, ad esempio per la ripetizione della formazione, usare l'indirizzo specifico dell'area geografica:
- https://asiasoutheast.management.azureml.net
- https://europewest.management.azureml.net

### Punti da notare

1.	In questo modo è possibile copiare solo esperimenti tra aree di lavoro che appartengono alla stessa area geografica. Se è necessario copiare un esperimento fra aree di lavoro che appartengono ad aree diverse, è possibile usare il commandlet di [PowerShell](http://aka.ms/amlps) [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment). Un'altra soluzione alternativa consiste nel pubblicare l'esperimento nella raccolta in modalità non in elenco, quindi aprirlo nell'area di lavoro dall'altra area.
2.	Il selettore dell’area visualizzerà solo le aree di lavoro di una determinata area geografica alla volta. In futuro, sarà possibile visualizzare un elenco completo delle aree di lavoro a cui si ha accesso contemporaneamente in più aree geografiche.
3.	Un’area di lavoro con accesso libero o Guest (anonimo) verrà creata e ospitata in Stati Uniti centro-meridionali. In futuro, sarà possibile creare aree di lavoro ad accesso libero/Guest nell'area che si desidera.
4.	I servizi Web distribuiti da un'area di lavoro nell’Asia sudorientale verranno anche ospitati in Asia sudorientale. In futuro, sarà possibile garantire la flessibilità della creazione di esperimenti in un'unica area e della distribuzione di endpoint generati del servizio web in aree diverse.

## Altre informazioni

Aggiungere una domanda sul [forum di Azure Machine Learning](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->  
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

<!---HONumber=AcomDC_0914_2016-->