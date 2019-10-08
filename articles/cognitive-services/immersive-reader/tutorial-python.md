---
title: 'Esercitazione: Avviare lo strumento di lettura immersiva con Python'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si creerà un'applicazione Python che avvia lo strumento di lettura immersiva.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 6404a5d49bd7af1ed5d74299f03eda8d0bb14b89
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326419"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Esercitazione: Avviare lo strumento di lettura immersiva usando il progetto di esempio Python

Nella [panoramica](./overview.md) si è appreso che cos'è lo strumento di lettura immersiva e come questo strumento implementa tecniche comprovate per migliorare la comprensione della lettura per studenti di lingue, lettori emergenti e studenti con differenze nell'apprendimento. Questa esercitazione descrive la creazione di un'applicazione Web Python che avvia lo strumento di lettura immersiva. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app Web Python con pip, Flask, Jinja e virtualenv usando un progetto di esempio
> * Acquisire un token di accesso
> * Avviare lo strumento di lettura immersiva con contenuto di esempio

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una risorsa Strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory (Azure AD). Seguire [queste istruzioni](./azure-active-directory-authentication.md) per configurare l'ambiente. Per la configurazione delle proprietà dell'ambiente, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [Git](https://git-scm.com/)
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) e [pip](https://docs.python.org/3/installing/index.html). A partire da Python 3,4, pip è incluso per impostazione predefinita con i programmi di installazione binari di Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win per Windows](https://pypi.org/project/virtualenvwrapper-win/) o [virtualenvwrapper per OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [Modulo requests](https://pypi.org/project/requests/2.7.0/)
* Un ambiente di sviluppo integrato, ad esempio [Visual Studio Code](https://code.visualstudio.com/)

## <a name="acquire-an-azure-ad-authentication-token"></a>Acquisire un token di autenticazione di Azure AD

Scrivere un'API back-end per recuperare un token di autenticazione di Azure AD.

Per questa parte sono necessari alcuni valori del passaggio relativo ai prerequisiti di configurazione dell'autenticazione di Azure AD. Fare riferimento al file di testo salvato della sessione.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Dopo aver recuperato i valori necessari, creare un nuovo file denominato _.env_ e incollare il codice seguente al suo interno, specificando i valori delle proprietà personalizzate dal passaggio precedente. Sostituire il file _.env_ nell'app di esempio con il nuovo file creato.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Fare attenzione a non eseguire il commit di questo file nel controllo del codice sorgente, poiché contiene segreti che non devono essere resi pubblici.

L'endpoint API **getimmersivereadertoken** deve essere protetto da qualche forma di autenticazione, ad esempio [OAuth](https://oauth.net/2/), per impedire agli utenti non autorizzati di ottenere token da usare per il servizio Strumento di lettura immersiva e la fatturazione, ma la relativa procedura esula dall'ambito di questa esercitazione.

## <a name="create-a-python-web-app-on-windows"></a>Creare un'app Web Python in Windows

Creare un'app Web Python usando `flask` in Windows.

Installare [Git](https://git-scm.com/).

Dopo l'installazione di Git, aprire un prompt dei comandi e immettere il comando "clone" per clonare il repository Git Immersive Reader SDK in una cartella del computer

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installare [Python](https://www.python.org/downloads/).

Selezionare la casella per l'aggiunta di Python a PATH.

![Finestra di dialogo di installazione di Python in Windows, passaggio 1](./media/pythoninstallone.jpg)

Aggiungere funzionalità facoltative selezionando le caselle e quindi fare clic sul pulsante "Avanti".

![Finestra di dialogo di installazione di Python in Windows, passaggio 2](./media/pythoninstalltwo.jpg)

Scegliere "Installazione personalizzata" e impostare il percorso di installazione come cartella radice, ad esempio `C:\Python37-32\`, e quindi fare clic sul pulsante "Installa".

![Finestra di dialogo di installazione di Python in Windows, passaggio 3](./media/pythoninstallthree.jpg)

Al termine dell'installazione di Python, aprire un prompt dei comandi e immettere `cd` per passare alla cartella Scripts di Python.

```cmd
cd C:\Python37-32\Scripts
```

Installare Flask.

```cmd
pip install flask
```

Installare Jinja2, un motore di modelli completo per Python.

```cmd
pip install jinja2
```

Installare virtualenv, uno strumento per creare ambienti Python isolati.

```cmd
pip install virtualenv
```

Installare virtualenvwrapper-win. L'idea alla base di virtualenvwrapper è semplificare l'uso di virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Installare il modulo requests. Si tratta di una libreria HTTP concessa in licenza da Apache2 e scritta in Python.

```cmd
pip install requests
```

Installare il modulo python-dotenv. Questo modulo legge la coppia chiave-valore dal file con estensione ENV e la aggiunge alla variabile di ambiente.

```cmd
pip install python-dotenv
```

Creare un ambiente virtuale

```cmd
mkvirtualenv advanced-python
```

Immettere `cd` per passare alla cartella radice del progetto di esempio.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Connettere il progetto di esempio all'ambiente. Viene eseguito il mapping dell'ambiente virtuale appena creato alla cartella radice del progetto di esempio.

```cmd
setprojectdir .
```

Attivare l'ambiente virtuale.

```cmd
activate
```

Il progetto è ora attivo e verrà visualizzato qualcosa di simile a `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` al prompt dei comandi.

Disattivare l'ambiente.

```cmd
deactivate
```

Il prefisso `(advanced-python)` non sarà più presente, in quanto l'ambiente è ora disattivato.

Per riattivare l'ambiente, eseguire `workon advanced-python` dalla cartella radice del progetto di esempio.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

Quando l'ambiente è attivo, eseguire il progetto di esempio immettendo `flask run` dalla relativa cartella radice.

```cmd
flask run
```

Aprire il browser e passare a _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Creare un'app Web Python in OSX

Creare un'app Web Python usando `flask` in OSX.

Installare [Git](https://git-scm.com/).

Al termine dell'installazione di Git, aprire il terminale ed eseguire il comando "clone" per clonare il repository Git Immersive Reader SDK in una cartella del computer

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installare [Python](https://www.python.org/downloads/).

La cartella radice di Python, `Python37-32`, sarà ora inclusa nella cartella Applicazioni.

Al termine dell'installazione di Python, aprire un prompt dei comandi e immettere `cd` per passare alla cartella degli script di Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installare pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Eseguire quindi il comando seguente per installare pip per l'utente attualmente connesso in modo da evitare problemi relativi alle autorizzazioni.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Immettere la password quando richiesto.
- Aggiungere il percorso dell'installazione di pip alla variabile PATH.
- Passare alla parte inferiore del file e immettere il percorso che si vuole aggiungere come ultimo elemento dell'elenco, ad esempio `PATH=$PATH:/usr/local/bin`.
- Premere CTRL+X per uscire.
- Immettere `Y` per salvare il buffer modificato.
- L'operazione è terminata. Per testare questi passaggi, in una nuova finestra del terminale digitare `echo $PATH`.

Installare Flask.

```bash
pip install flask --user
```

Installare Jinja2, un motore di modelli completo per Python.

```bash
pip install Jinja2 --user
```

Installare virtualenv, uno strumento per creare ambienti Python isolati.

```bash
pip install virtualenv --user
```

Installare virtualenvwrapper. L'idea alla base di virtualenvwrapper è semplificare l'uso di virtualenv.

```bash
pip install virtualenvwrapper --user
```

Installare il modulo requests. Si tratta di una libreria HTTP concessa in licenza da Apache2 e scritta in Python.

```bash
pip install requests --user
```

Installare il modulo python-dotenv. Questo modulo legge la coppia chiave-valore dal file con estensione ENV e la aggiunge alla variabile di ambiente.

```bash
pip install python-dotenv --user
```

Scegliere una cartella in cui mantenere gli ambienti virtuali ed eseguire questo comando

```bash
mkdir ~/.virtualenvs
```

Immettere `cd` per passare alla cartella dell'applicazione di esempio Python in Immersive Reader SDK.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Creare un ambiente virtuale

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Connettere il progetto di esempio all'ambiente. Viene eseguito il mapping dell'ambiente virtuale appena creato alla cartella radice del progetto di esempio.

```bash
setprojectdir .
```

Attivare l'ambiente virtuale.

```bash
activate
```

Il progetto è ora attivo e verrà visualizzato qualcosa di simile a `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` al prompt dei comandi.

Disattivare l'ambiente.

```bash
deactivate
```

Il prefisso `(advanced-python)` non sarà più presente, in quanto l'ambiente è ora disattivato.

Per riattivare l'ambiente, eseguire `workon advanced-python` dalla cartella radice del progetto di esempio.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

Quando l'ambiente è attivo, eseguire il progetto di esempio immettendo `flask run` dalla relativa cartella radice.

```bash
flask run
```

Aprire il browser e passare a _http://localhost:5000_ .

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md)
* Visualizzare gli esempi di codice su [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
