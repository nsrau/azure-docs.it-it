<properties
   pageTitle="Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico | Microsoft Azure"
   description="In questo articolo vengono fornite istruzioni per scegliere il nome di dominio aziendale per un nome di dominio di Gestione traffico."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico di Azure

Per scegliere il nome di domino aziendale per un nome di dominio di Gestione traffico, modificare il record di risorse DNS sul server DNS Internet per utilizzare il tipo di record CNAME. Quest'ultimo mappa il nome di dominio aziendale per il nome di dominio del profilo di Gestione traffico. È possibile visualizzare il nome di dominio di Gestione traffico nella sezione **Generale** nella pagina di configurazione del profilo di Gestione traffico.

Ad esempio, per scegliere il nome di dominio aziendale www.contoso.com per il nome di dominio di Gestione traffico contoso.trafficmanager.net, è possibile aggiornare il record di risorse DNS nel seguente modo:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Tutto il traffico indirizzato a *www.contoso.com* verrà ora reindirizzato a *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT]Non è possibile scegliere un dominio di secondo livello, come *contoso.com*, per il dominio di Gestione traffico. Si tratta del limite del protocollo DNS, che non consente record CNAME per i nomi di dominio di secondo livello.

## Passaggi successivi

[Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)

[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)

<!---HONumber=Nov15_HO4-->