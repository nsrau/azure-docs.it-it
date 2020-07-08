---
title: Valutazione dell'equità dei modelli ML in Python
titleSuffix: Azure Machine Learning
description: Informazioni su come valutare l'equità dei modelli in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 06/30/2020
ms.custom: tracking-python
ms.openlocfilehash: 9e88b87a7f6471f6c7344cc5548c37e947f18791
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85660796"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models"></a>Usare Azure Machine Learning con il pacchetto open source Fairlearn per valutare l'equità dei modelli ML  

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida dettagliata si apprenderà come usare il pacchetto python open source di [Fairlearn](https://fairlearn.github.io/) con Azure Machine Learning per eseguire le attività seguenti:

* Valutare l'equità delle stime del modello. Per altre informazioni sull'equità in Machine Learning, vedere l' [articolo sull'equità in Machine Learning](concept-fairness-ml.md).
* Consente di caricare, elencare e scaricare informazioni sull'equità della valutazione da e verso Azure Machine Learning Studio.
* Per interagire con le informazioni sull'equità dei modelli, vedere un dashboard di valutazione dell'equità in Azure Machine Learning Studio.

>[!NOTE]
> La valutazione dell'equità non è un esercizio puramente tecnico. **Questo pacchetto consente di valutare l'equità di un modello di apprendimento automatico, ma è possibile solo configurare e prendere decisioni relative al modo in cui il modello viene eseguito.**  Sebbene questo pacchetto consenta di identificare le metriche quantitative per valutare l'equità, gli sviluppatori di modelli di Machine Learning devono anche eseguire un'analisi qualitativa per valutare l'equità dei propri modelli.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning Equity SDK 

Il Azure Machine Learning Equity SDK, `azureml-contrib-fairness` , integra il pacchetto python open source, [Fairlearn](http://fairlearn.github.io), all'interno di Azure Machine Learning. Per altre informazioni sull'integrazione di Fairlearn in Azure Machine Learning, vedere questi [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Per ulteriori informazioni su Fairlearn, vedere la [Guida di esempio](https://fairlearn.github.io/auto_examples/notebooks/index.html) e i [notebook di esempio](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Usare i comandi seguenti per installare i `azureml-contrib-fairness` `fairlearn` pacchetti e:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Caricare informazioni sull'equità per un singolo modello

L'esempio seguente illustra come usare il pacchetto di correttezza per caricare informazioni dettagliate sull'equità del modello in Azure Machine Learning e vedere il dashboard di valutazione dell'equità in Azure Machine Learning Studio.

1. Eseguire il training di un modello di esempio in un notebook di Jupyter. 

    Per il set di dati viene usato il noto set di dati del censimento per adulti, che viene caricato usando `shap` (per praticità). Ai fini di questo esempio, questo set di dati viene trattato come un problema di decisione di prestito e si Finge che l'etichetta indichi se ogni singolo utente ha ripagato un prestito nel passato. I dati vengono usati per eseguire il training di un predittore per stimare se gli utenti non rilevati in precedenza ripagheranno un prestito. Il presupposto è che le stime del modello vengano utilizzate per decidere se a un utente deve essere offerto un prestito.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Accedere a Azure Machine Learning e registrare il modello.
   
    Il dashboard di equità può integrarsi con i modelli registrati o non registrati. Registrare il modello in Azure Machine Learning con i passaggi seguenti:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_linear_regression", unmitigated_predictor)
    ```

3. Pre-calcola le metriche di equità.

    Creare un dizionario del dashboard usando il `metrics` pacchetto di Fairlearn. Il `_create_group_metric_set` metodo ha argomenti simili al costruttore del dashboard, ad eccezione del fatto che le funzionalità sensibili vengono passate come dizionario (per garantire che i nomi siano disponibili). È inoltre necessario specificare il tipo di stima (classificazione binaria in questo caso) quando si chiama questo metodo.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = unmitigated_predictor.predict(X_test)
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Caricare le metriche di equità pre-calcolate.
    
    Importare ora `azureml.contrib.fairness` il pacchetto per eseguire il caricamento:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Creare un esperimento, quindi un'esecuzione e caricarvi il Dashboard:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Verificare il dashboard di equità da Azure Machine Learning Studio

    Se si completano i passaggi precedenti (caricamento di informazioni sull'equità generate per Azure Machine Learning), è possibile visualizzare il dashboard di equità in [Azure Machine Learning Studio](https://ml.azure.com). Questo dashboard è lo stesso dashboard di visualizzazione fornito in Fairlearn, che consente di analizzare le differenze tra i sottogruppi della funzionalità sensibile, ad esempio, maschio e femmina.
    Seguire uno di questi percorsi per accedere al dashboard di visualizzazione in Azure Machine Learning Studio:

    * **Riquadro esperimenti (anteprima)**
    1. Selezionare **Experiments (esperimenti** ) nel riquadro a sinistra per visualizzare un elenco di esperimenti eseguiti in Azure Machine Learning.
    1. Selezionare un esperimento specifico per visualizzare tutte le esecuzioni dell'esperimento.
    1. Selezionare un'esecuzione e quindi la scheda **equità** per visualizzare il dashboard spiegazione.


    [![Dashboard di equità](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Riquadro modelli**
    1. Se il modello originale è stato registrato seguendo i passaggi precedenti, è possibile selezionare **modelli** nel riquadro a sinistra per visualizzarlo.
    1. Selezionare un modello e quindi la scheda **equità** per visualizzare il dashboard di visualizzazione spiegazione.

    Per altre informazioni sul dashboard di visualizzazione e sul contenuto, vedere il [manuale dell'utente](https://fairlearn.github.io/user_guide/assessment.html#fairlearn-dashboard)di Fairlearn.

s # # caricare informazioni sull'equità per più modelli

Se si è interessati a confrontare più modelli e a capire in che modo le loro valutazioni sull'equità sono diverse, è possibile passare più di un modello al dashboard di visualizzazione ed esplorare i compromessi relativi alle prestazioni.

1. Eseguire il training dei modelli:
    
    Oltre al modello di regressione logistica precedente, viene ora creato un secondo classificatore, basato su uno strumento di stima del vettore di supporto e viene caricato un dizionario di dashboard equità usando il pacchetto di Fairlearn `metrics` . Si noti che in questo caso verranno ignorati i passaggi per caricare e pre-elaborare i dati e passare direttamente alla fase di training del modello.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Registrare i modelli

    Registrare quindi entrambi i modelli all'interno Azure Machine Learning. Per praticità nelle chiamate al metodo successive, archiviare i risultati in un dizionario, che esegue il mapping dell'oggetto `id` del modello registrato (una stringa nel `name:version` formato) al predittore stesso:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_linear_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Caricare il dashboard di Fairlearn localmente

    Prima di caricare le informazioni sull'equità in Azure Machine Learning, è possibile esaminare queste stime in un dashboard Fairlearn richiamato localmente. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Pre-calcola le metriche di equità.

    Creare un dizionario del dashboard usando il `metrics` pacchetto di Fairlearn.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Caricare le metriche di equità pre-calcolate.
    
    Importare ora `azureml.contrib.fairness` il pacchetto per eseguire il caricamento:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Creare un esperimento, quindi un'esecuzione e caricarvi il Dashboard:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Analogamente alla sezione precedente, è possibile seguire uno dei percorsi descritti in precedenza (tramite **esperimenti** o **modelli**) in Azure Machine Learning Studio per accedere al dashboard di visualizzazione e confrontare i due modelli in termini di equità e prestazioni.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Caricare informazioni dettagliate sull'equità non mitigate e mitigate

È possibile usare gli [algoritmi di mitigazione](https://fairlearn.github.io/user_guide/mitigation.html)di Fairlearn, confrontare i modelli di mitigazione generati con il modello originale non mitigato ed esplorare i compromessi di prestazioni/equità tra i modelli confrontati.

Per un esempio in cui viene illustrato l'uso dell'algoritmo di mitigazione della [ricerca nella griglia](https://fairlearn.github.io/user_guide/mitigation.html#grid-search) (che consente di creare una raccolta di modelli mitigati con diversi compromessi in materia di equità e prestazioni), vedere questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Il caricamento delle informazioni sull'equità di più modelli in un'unica esecuzione consentirebbe il confronto dei modelli rispetto all'equità e alle prestazioni. È possibile fare clic su uno dei modelli visualizzati nel grafico di confronto del modello per visualizzare informazioni dettagliate sull'equità del modello specifico.


[![Dashboard di confronto del modello Fairlearn](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più sull'equità dei modelli](concept-fairness-ml.md)

[Estrai notebook di esempio Azure Machine Learning equità](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
