<properties
   pageTitle="Disabilitare o abilitare un endpoint di Gestione traffico | Microsoft Azure"
   description="Questo articolo aiuterà a disabilitare o abilitare gli endpoint del profilo di Gestione traffico."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Disabilitare o abilitare un endpoint di Gestione traffico

È anche possibile disabilitare singoli endpoint che appartengono a un profilo di Gestione traffico. Gli endpoint includono servizi cloud e siti Web. Quando si disabilita un endpoint questo continua a far parte del profilo, sebbene quest'ultimo si comporti come se non fosse incluso. Questa azione è particolarmente utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo.

>[AZURE.NOTE] **La disabilitazione di un endpoint non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un endpoint integro rimane attivo e in grado di ricevere traffico anche se è disabilitato in Gestione traffico. La disabilitazione di un endpoint in un profilo non influisce sul relativo stato in un altro profilo.**

## Per disabilitare un endpoint

1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico che contiene le impostazioni degli endpoint da modificare e quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
1. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoints** nella parte superiore della pagina. 
1. Fare clic sull'endpoint da disabilitare, quindi fare clic su **Disabilita** nella parte inferiore della pagina.
1. Il traffico indirizzato all'endpoint verrà arrestato in base alla durata (TTL) del DNS configurato per il nome di dominio di Gestione traffico. È possibile modificare la durata (TTL) dalla pagina di configurazione del profilo di Gestione traffico.

## Per abilitare un endpoint


1. Nel riquadro di Gestione traffico nel portale di Azure classico trovare il profilo di Gestione traffico che contiene le impostazioni degli endpoint da modificare e quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
1. Per visualizzare gli endpoint inclusi nella configurazione, fare clic su **Endpoints** nella parte superiore della pagina.
1. Fare clic sull'endpoint da abilitare, quindi fare clic su **Abilita** nella parte inferiore della pagina.
1. Il traffico indirizzato al servizio ospitato riprenderà di nuovo secondo il profilo.

## Passaggi successivi

[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)

[Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)

<!---HONumber=AcomDC_0323_2016-->