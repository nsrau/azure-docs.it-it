<properties 
   pageTitle="Come usare Cache Redis di Azure con Python" 
   description="Introduzione all'uso di Cache Redis di Azure con Python" 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/19/2015"
   ms.author="mwasson"/>

# Come usare Cache Redis di Azure con Python 

Questo argomento descrive come usare Cache Redis di Azure con Python.


## Prerequisiti

Installare [redis-py](https://github.com/andymccurdy/redis-py).


## Creare una cache Redis in Azure

Nella [versione di anteprima del portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo** e selezionare **Cache Redis**. 

  ![][1]

Immettere un nome host DNS nel formato `<nome>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]

Dopo aver creato la cache, fare clic su di essa nel portale per visualizzarne le impostazioni. Saranno necessari:

- **Nome host.** Si tratta del nome immesso al momento della creazione della cache.
- **Porta.** Fare clic sul collegamento sotto **Porte** per visualizzare le porte. Usare la porta SSL. 
- **Chiave di accesso.** Fare clic sul collegamento sotto **Chiavi** e copiare la chiave primaria.

## Aggiungere dati alla cache e recuperarli

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'
    
Sostituire *&lt;name&gt;* con il nome della cache e *&lt;key&gt;* con la chiave di accesso. 


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png


<!--HONumber=49-->