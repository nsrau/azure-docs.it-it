---
title: Verificare le impostazioni di Gestione traffico di Azure | Microsoft Docs
description: Questo articolo contiene le informazioni necessarie per verificare le impostazioni di Gestione traffico.
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 2180b640-596e-4fb2-be59-23a38d606d12
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: aadff1806a7cb22347283143563467366e857569
ms.contentlocale: it-it
ms.lasthandoff: 03/18/2017

---

# <a name="verify-traffic-manager-settings"></a>Verificare le impostazioni di Gestione traffico

Per verificare le impostazioni di Gestione traffico, è necessario disporre di più client, ubicati in diverse posizioni, da cui eseguire i test. Arrestare quindi uno alla volta gli endpoint del profilo di Gestione traffico.

* Impostare una durata (TTL) DNS bassa, ad esempio 30 secondi, per consentire una rapida propagazione delle modifiche.
* Conoscere gli indirizzi IP dei servizi cloud e dei siti Web di Azure nel profilo in corso di verifica.
* Utilizzare gli strumenti che consentono di risolvere un nome DNS per un indirizzo IP e visualizzare tale indirizzo.

Eseguire un controllo per verificare che i nomi DNS vengano risolti negli indirizzi IP degli endpoint nel profilo. La risoluzione dei nomi deve avvenire in modo coerente con il metodo di routing del traffico definito nel profilo di Gestione traffico. Per risolvere i nomi DNS è possibile usare strumenti come **nslookup** o **dig**.

Per testare il profilo di Gestione traffico, vedere gli esempi seguenti.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Controllare il profilo di Gestione traffico in Windows con nslookup e ipconfig

1. Aprire un comando o prompt Windows PowerShell come amministratore.
2. Digitare `ipconfig /flushdns` per scaricare la cache del resolver DNS.
3. Digitare `nslookup <your Traffic Manager domain name>`. Il comando seguente, ad esempio, controlla il nome di dominio con il prefisso *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Un risultato tipico visualizza le informazioni seguenti:

    + Nome DNS e indirizzo IP del server DNS a cui si accede per risolvere questo nome di dominio di Gestione traffico.
    + Nome del dominio di Gestione traffico digitato nella riga di comando dopo "nslookup" e indirizzo IP in cui viene risolto il dominio di Gestione traffico. Il secondo indirizzo IP è quello più importante da verificare. Deve corrispondere a un indirizzo VIP per uno dei servizi cloud o dei siti Web nel profilo di Gestione traffico in fase di verifica.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Come testare il metodo di routing del traffico failover

1. Lasciare tutti gli endpoint attivi.
2. Usando un unico client, richiedere la risoluzione DNS per il nome di dominio aziendale mediante nslookup o un'utilità simile.
3. Verificare che l'indirizzo IP risolto corrisponda all'endpoint primario.
4. Arrestare l'endpoint primario o rimuovere il file di monitoraggio in modo che Gestione traffico consideri l'applicazione inattiva.
5. Attendere la durata (TTL) DNS del profilo di Gestione traffico più altri due minuti. Ad esempio, se la durata TTL del DNS è 300 secondi (5 minuti), è necessario attendere sette minuti.
6. Scaricare la cache del client DNS e richiedere una risoluzione DNS usando nslookup. In Windows è possibile scaricare la cache DNS con il comando ipconfig /flushdns.
7. Verificare che l'indirizzo IP risolto corrisponda all'endpoint secondario.
8. Ripetere la procedura arrestando gli endpoint uno dopo l'altro. Verificare che il DNS restituisca l'indirizzo IP del successivo endpoint dell'elenco. Quando tutti gli endpoint sono inattivi, si dovrebbe ottenere nuovamente l'indirizzo IP dell'endpoint primario.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Come testare il metodo di routing del traffico ponderato

1. Lasciare tutti gli endpoint attivi.
2. Usando un unico client, richiedere la risoluzione DNS per il nome di dominio aziendale mediante nslookup o un'utilità simile.
3. Verificare che l'indirizzo IP risolto corrisponda a uno degli endpoint.
4. Scaricare la cache del client DNS e continuare a ripetere i passaggi 2 e 3 per ciascun endpoint. Vengono visualizzati i diversi indirizzi IP restituiti per ognuno degli endpoint.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Come testare il metodo di routing del traffico delle prestazioni

Per verificare in modo efficace il metodo di routing del traffico delle prestazioni, è necessario che i client si trovino in diverse parti del mondo. È possibile creare client in diverse aree di Azure utilizzabili per testare i servizi. Se si dispone di una rete globale, è possibile accedere in remoto a client ubicati in altri paesi ed eseguire quindi i test da tali client.

In alternativa, sono disponibili servizi gratuiti di analisi approfondita e DNS basati su Web. Alcuni di questi strumenti consentono di verificare la risoluzione del nome DNS da diverse località del mondo. Per alcuni esempi, eseguire una ricerca con le parole chiave "Ricerca DNS". È possibile usare servizi di terze parti come Gomez o Keynote per confermare che i profili distribuiscano il traffico nel modo previsto.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)
* [Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)
* [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)

