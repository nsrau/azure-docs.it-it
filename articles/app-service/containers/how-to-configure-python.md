---
title: Configurare le immagini Python predefinite nel Servizio app di Azure
description: Questa esercitazione descrive le opzioni per la creazione e la configurazione di un'applicazione Python sul Servizio app di Azure con l’immagine Python predefinita.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228553"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>Configurare le immagini Python predefinite nel Servizio app di Azure (Anteprima)

Questo articolo illustra come configurare l'immagine incorporata di Python nel [Servizio app di Azure in Linux](app-service-linux-intro.md) per eseguire le applicazioni Python.

## <a name="python-version"></a>Versione di Python

Il runtime di Python nel servizio app di Azure in Linux usa la versione `python-3.7.0`.

## <a name="supported-frameworks"></a>Framework supportati

Sono compatibili utte le versioni di Web Server Gateway Interface(WSGI) conformi con i framework di Web compatibili con il `python-3.7` runtime.

## <a name="package-management"></a>Gestione dei pacchetti

Durante la pubblicazione Git, il motore Kudu cerca [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) nella radice del repository e in automatico installa i pacchetti in Azure usando `pip`.

Per generare questo file prima della pubblicazione, passare alla radice del repository ed eseguire il comando seguente nell'ambiente Python:

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>Configurare l'app Python

L'immagine predefinita di Python nel servizio app usa la [Gunicorn](http://gunicorn.org/), server per eseguire l'applicazione Python. Gunicorn è un Server HTTP WSGI di Python per UNIX. Il servizio app consente di automatizzare la configurazione automatica di Gunicorn per i progetti Django e Flask.

### <a name="django-app"></a>App Django

Se si pubblica un progetto Django che contiene un `wsgi.py` modulo, Azure chiama in automatico Gunicorn usando il comando seguente:

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>App Flask

Se si pubblica un'app Flask e il punto di ingresso è un modulo `application.py` o `app.py`, Azure chiama in automatico Gunicorn usando uno dei comandi seguenti, rispettivamente:

```bash
gunicorn application:app
```

Oppure 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>Personalizzare l’avvio

Per definire un punto di ingresso personalizzato per l'app, creare prima di tutto un file _.txt_ con un comando Gunicorn personalizzato e inserirlo nella radice del progetto. Ad esempio, per avviare il server con il modulo _helloworld.py_ e la variabile `app`, creare un _startup.txt_ con il contenuto seguente:

```bash
gunicorn helloworld:app
```

Nella pagina [Impostazioni dell'applicazione](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), selezionare **Python |3.7** come lo **Stack di Runtime** e specificare il nome del **File di avvio** dal passaggio precedente. Ad esempio, _startup.txt_.
