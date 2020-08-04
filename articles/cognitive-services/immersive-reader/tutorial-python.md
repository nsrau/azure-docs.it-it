---
title: 'Esercitazione: Avviare lo strumento di lettura immersiva con Python'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione verrà creata un'applicazione Python che avvia lo strumento di lettura immersiva.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.custom: tracking-python
ms.openlocfilehash: 93a1ac9d6a82997ec7552341eb4829728e8471fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076903"
---
# <a name="tutorial-start-the-immersive-reader-using-the-python-sample-project"></a>Esercitazione: Avviare lo strumento di lettura immersiva usando il progetto di esempio per Python

Nella [panoramica](./overview.md) si è appreso che cos'è lo strumento di lettura immersiva e come questo strumento implementa tecniche comprovate per migliorare la comprensione della lettura per studenti di lingue, lettori emergenti e studenti con differenze nell'apprendimento. Questa esercitazione descrive come creare un'applicazione Web Python che avvia lo strumento di lettura immersiva. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'app Web Python con pip, Flask, Jinja e virtualenv usando un progetto di esempio.
> * Acquisire un token di accesso.
> * Avviare lo strumento di lettura immersiva con contenuto di esempio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](./how-to-create-immersive-reader.md) per configurare l'ambiente. Quando si configurano le proprietà dell'ambiente, sarà necessario usare alcuni valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [Git](https://git-scm.com/).
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) e [pip](https://docs.python.org/3/installing/index.html). A partire da Python 3,4, pip è incluso per impostazione predefinita con i programmi di installazione binari di Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/).
* [Jinja](http://jinja.pocoo.org/docs/2.10/).
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win per Windows](https://pypi.org/project/virtualenvwrapper-win/) o [virtualenvwrapper per OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* [Modulo requests](https://pypi.org/project/requests/2.7.0/).
* Un ambiente di sviluppo integrato, ad esempio [Visual Studio Code](https://code.visualstudio.com/).

## <a name="configure-authentication-credentials"></a>Configurare le credenziali di autenticazione

Creare un nuovo file denominato **.env**e incollare i nomi e i valori seguenti al suo interno. Specificare i valori forniti quando è stata creata la risorsa dello strumento di lettura immersiva.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Non eseguire il commit di questo file nel controllo del codice sorgente, perché contiene segreti che non devono essere resi pubblici.

Proteggere l'endpoint API **getimmersivereadertoken** tramite una forma di autenticazione, ad esempio [OAuth](https://oauth.net/2/). L'autenticazione impedisce agli utenti non autorizzati di ottenere i token da usare per il servizio di lettura immersiva e la fatturazione, tuttavia questa attività non rientra nell'ambito di questa esercitazione.

## <a name="create-a-python-web-app-on-windows"></a>Creare un'app Web Python in Windows

Creare un'app Web Python usando `flask` in Windows.

Installare [Git](https://git-scm.com/).

Dopo l'installazione di Git, aprire un prompt dei comandi e clonare il repository Git Immersive Reader SDK in una cartella del computer.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installare [Python](https://www.python.org/downloads/).

Selezionare la casella di controllo **Add Python to PATH** (Aggiungi Python a PATH).

![Finestra di dialogo di installazione di Python in Windows, passaggio 1](./media/pythoninstallone.jpg)

Aggiungere le **Funzionalità facoltative** selezionando le caselle di controllo, quindi fare clic su **Avanti**.

![Finestra di dialogo di installazione di Python in Windows, passaggio 2](./media/pythoninstalltwo.jpg)

Scegliere **Installazione personalizzata** e impostare il percorso di installazione sul percorso della cartella radice, ad esempio `C:\Python37-32\`. Selezionare quindi **Installa**.

![Finestra di dialogo di installazione di Python in Windows, passaggio 3](./media/pythoninstallthree.jpg)

Al termine dell'installazione di Python, aprire un prompt dei comandi e digitare `cd` per passare alla cartella Scripts di Python.

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

Installare il modulo python-dotenv. Questo modulo legge la coppia chiave-valore dal file ENV e la aggiunge alla variabile di ambiente.

```cmd
pip install python-dotenv
```

Creare un ambiente virtuale.

```cmd
mkvirtualenv advanced-python
```

Digitare `cd` per passare alla cartella radice del progetto di esempio.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Connettere il progetto di esempio all'ambiente. Questa azione esegue il mapping dell'ambiente virtuale appena creato alla cartella radice del progetto di esempio.

```cmd
setprojectdir .
```

Attivare l'ambiente virtuale.

```cmd
activate
```

Il progetto è ora attivo e verrà visualizzato un testo simile a `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` nel prompt dei comandi.

Disattivare l'ambiente.

```cmd
deactivate
```

Il prefisso `(advanced-python)` non sarà più presente, in quanto l'ambiente è ora disattivato.

Per riattivare l'ambiente, eseguire `workon advanced-python` dalla cartella radice del progetto di esempio.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

Quando l'ambiente è attivo, eseguire il progetto di esempio immettendo `flask run` dalla relativa cartella radice.

```cmd
flask run
```

Aprire il browser e passare a http://localhost:5000.

## <a name="create-a-python-web-app-on-osx"></a>Creare un'app Web Python in OSX

Creare un'app Web Python usando `flask` in OSX.

Installare [Git](https://git-scm.com/).

Al termine dell'installazione di Git, aprire il terminale e clonare il repository Git Immersive Reader SDK in una cartella del computer.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installare [Python](https://www.python.org/downloads/).

La cartella radice di Python, ad esempio `Python37-32`, sarà ora inclusa nella cartella Applicazioni.

Al termine dell'installazione di Python, aprire il terminale e digitare `cd` per passare alla cartella Scripts di Python.

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
- Selezionare **CTRL+X** per uscire.
- Immettere **Y** per salvare il buffer modificato.
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

Installare il modulo python-dotenv. Questo modulo legge la coppia chiave-valore dal file ENV e la aggiunge alla variabile di ambiente.

```bash
pip install python-dotenv --user
```

Scegliere una cartella in cui conservare gli ambienti virtuali ed eseguire questo comando:

```bash
mkdir ~/.virtualenvs
```

Digitare `cd` per passare alla cartella dell'applicazione di esempio Python in Immersive Reader SDK.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Creare un ambiente virtuale.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Connettere il progetto di esempio all'ambiente. Questa azione esegue il mapping dell'ambiente virtuale appena creato alla cartella radice del progetto di esempio.

```bash
setprojectdir .
```

Attivare l'ambiente virtuale.

```bash
activate
```

Il progetto è ora attivo e verrà visualizzato un testo simile a `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` nel prompt dei comandi.

Disattivare l'ambiente.

```bash
deactivate
```

Il prefisso `(advanced-python)` non sarà più presente, in quanto l'ambiente è ora disattivato.

Per riattivare l'ambiente, eseguire `workon advanced-python` dalla cartella radice del progetto di esempio.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

Quando l'ambiente è attivo, eseguire il progetto di esempio immettendo `flask run` dalla relativa cartella radice.

```bash
flask run
```

Aprire il browser e passare a http://localhost:5000.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](./reference.md).
* Visualizzare gli esempi di codice in [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
