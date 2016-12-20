---
title: Registrazione per i servizi Web di Machine Learning | Documentazione Microsoft
description: Informazioni su come abilitare la registrazione per i servizi Web di Machine Learning. La registrazione fornisce informazioni aggiuntive per risolvere i problemi relativi alle API.
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/05/2016
ms.author: raymondl;garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f307a26bfbb55b395f4073f4368432ae69b867ae


---
# <a name="enable-logging-for-machine-learning-web-services"></a>Abilitare la registrazione per i servizi Web di Machine Learning
Questo documento include informazioni sulla funzionalità di registrazione dei servizi Web classici. L'abilitazione della registrazione nei servizi Web fornisce informazioni aggiuntive per la risoluzione dei problemi delle API di Machine Learning, oltre a un semplice codice di errore e a un messaggio.  

Per abilitare la registrazione ai servizi Web nel portale di Azure classico:   

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Nella colonna delle funzionalità a sinistra fare clic su **MACHINE LEARNING**.
3. Fare clic sull'area di lavoro personale, quindi su **Servizi Web**.
4. Nell'elenco dei servizi Web fare clic sul nome del servizio Web scelto.
5. Nell'elenco di endpoint fare clic sul nome dell'endpoint scelto.
6. Fare clic su **CONFIGURA**.
7. Impostare **Livello di traccia diagnostica** su *Errore* o *Tutti*, quindi fare clic su **Salva**.

Per abilitare la registrazione nel portale dei servizi Web di Azure Machine Learning.

1. Accedere al portale [dei servizi Web di Azure Machine Learning](https://services.azureml.net).
2. Fare clic su Classic Web Services (Servizi Web classici).
3. Nell'elenco dei servizi Web fare clic sul nome del servizio Web scelto.
4. Nell'elenco di endpoint fare clic sul nome dell'endpoint scelto.
5. Fare clic su **Configura**.
6. Impostare **Registrazione** su *Errore* o su *Tutti*, quindi fare clic su **Salva**.

## <a name="the-effects-of-enabling-logging"></a>Effetti dell'abilitazione della registrazione
Quando la registrazione è abilitata, tutti i dati di diagnostica e gli errori originati dall'endpoint selezionato vengono registrati nell'account di archiviazione di Azure collegato all'area di lavoro dell'utente. È possibile visualizzare questo account di archiviazione nella visualizzazione Dashboard del portale di Azure classico (nella parte inferiore della sezione Riepilogo rapido) dell'area di lavoro.  

I log possono essere visualizzati tramite uno dei diversi strumenti disponibili per l'esplorazione degli account di archiviazione di Azure. Il modo più semplice per visualizzare i log è passare all'account di archiviazione nel portale di Azure classico e quindi fare clic su **Contenitori**. Verrà quindi visualizzato un contenitore denominato **ml-diagnostics**. Tale contenitore contiene tutte le informazioni di diagnostica per tutti gli endpoint del servizio Web di tutte le aree di lavoro associate all'account di archiviazione. 

## <a name="log-blob-detail-information"></a>Informazioni dettagliate dei BLOB dei log
Ogni BLOB nel contenitore contiene le informazioni di diagnostica per uno degli elementi seguenti:

* Un'esecuzione del metodo Batch-Execution  
* Un'esecuzione del metodo Request-Response  
* Inizializzazione di un contenitore Request-Response

Il nome di ogni BLOB riporta un prefisso nel formato seguente: 

{ID area di lavoro}-{ID servizio Web}-{ID endpoint}/{Tipo log}  

Dove Tipo log corrisponde a uno dei seguenti valori:  

* batch  
* punteggio/richieste  
* punteggio/init  




<!--HONumber=Nov16_HO3-->


