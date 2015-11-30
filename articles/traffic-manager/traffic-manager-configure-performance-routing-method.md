<properties 
   pageTitle="Configurare un metodo di routing del traffico delle prestazioni | Microsoft Azure"
   description="Questo articolo aiuterà a configurare il metodo di routing del traffico failover in Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# Configurare un metodo di routing del traffico delle prestazioni

Per eseguire il routing del traffico per i servizi cloud e siti Web (endpoint) che si trovano in diversi data center (noti anche come aree) in tutto il mondo, è possibile indirizzare il traffico in ingresso proveniente dal client che effettua la richiesta all'endpoint con latenza più bassa. In genere, il data center con latenza più bassa corrisponde a quello più vicino in termini di distanza geografica. Il metodo di routing del traffico basato sulle prestazioni consente di distribuire il carico in base alla latenza più bassa, ma non può tenere conto delle modifiche in tempo reale della configurazione della rete o del carico. Per altre informazioni sui diversi metodi di routing del traffico forniti da Gestione traffico di Azure, vedere [Informazioni sui metodi di routing del traffico di Gestione Traffico](traffic-manager-load-balancing-methods.md).

## Eseguire il routing del traffico in base alla latenza più bassa in un set di endpoint:

1. Nel riquadro sinistro del portale di gestione fare clic sull'icona **Gestione traffico** per aprire il relativo riquadro. Se non è ancora stato creato il profilo di Gestione traffico, vedere [Gestire i profili di Gestione traffico](traffic-manager-manage-profiles.md) per i passaggi necessari per la creazione di un profilo di base di Gestione traffico.
2. Nel riquadro di Gestione traffico nel portale di gestione individuare il profilo di Gestione traffico in cui sono contenute le impostazioni che si desidera modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
3. Nella parte superiore della pagina del profilo fare clic su **Endpoint** e verificare che siano presenti gli endpoint servizio che si desidera includere nella configurazione. Per i passaggi necessari per aggiungere o rimuovere endpoint dal profilo, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).
4. Nella parte superiore della pagina del profilo fare clic su **Configura** per aprire la pagina di configurazione.
5. Per **Impostazioni del metodo di routing del traffico**, verificare che il metodo di bilanciamento del carico sia **Prestazioni*. In caso contrario, fare clic su **Prestazioni** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** siano configurate correttamente. Tramite il monitoraggio viene assicurato il mancato invio di traffico agli endpoint offline. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Si noti che la barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita). Per altre informazioni sul monitoraggio, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche apportate alla configurazione. Per altre informazioni, vedere [Test delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md).
9. Dopo avere impostato e verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico. Per altre informazioni, vedere [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).

## Passaggi successivi


[Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md)

[Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md)

[Configurare metodo di routing failover](traffic-manager-configure-failover-routing-method.md)

[Configurare il metodo di routing Round Robin](traffic-manager-configure-round-robin-routing-method.md)

[Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)

[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)
 

<!---HONumber=Nov15_HO4-->