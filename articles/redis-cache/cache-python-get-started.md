<properties
	pageTitle="Come usare Cache Redis di Azure con Python | Microsoft Azure"
	description="Introduzione all'uso di Cache Redis di Azure con Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/25/2015"
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure con Python

Questo argomento descrive come usare Cache Redis di Azure con Python.


## Prerequisiti

Installare [redis-py](https://github.com/andymccurdy/redis-py).


## Creare una cache Redis in Azure

Nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo**, **Dati + Archiviazione** e selezionare **Cache Redis**.

  ![][1]

Immettere un nome host DNS Avrà il formato `<name>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]

Dopo aver creato la cache, fare clic su di essa nel portale di anteprima per visualizzarne le impostazioni. Saranno necessari:

- **Nome host.** Si tratta del nome immesso al momento della creazione della cache.
- **Porta.** Fare clic sul collegamento in **Porte** per visualizzare le porte. Utilizzare la porta SSL.
- **Chiave di accesso.** Fare clic in **Chiavi** e copiare la chiave principale.

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

<!---HONumber=August15_HO9-->