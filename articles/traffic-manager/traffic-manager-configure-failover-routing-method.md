<properties 
   pageTitle="Gestione un metodo di routing del traffico failover di Gestione traffico | Microsoft Azure"
   description="Questo articolo aiuterà a configurare il metodo di routing del traffico failover in Gestione traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="joaoma" />

# Configurare metodo di routing failover

In genere le organizzazioni desiderano offrire la massima affidabilità per i servizi erogati. A questo scopo, è necessario fornire servizi di backup in caso di inattività del servizio primario. Un modello comune di failover del servizio consiste nel fornire un set di servizi identici e nell'inviare traffico a un servizio primario gestendo, al contempo, un elenco configurato di uno o più servizi di backup. Per configurare questo tipo di backup con i servizi cloud e i siti Web di Azure, attenersi alla procedura riportata di seguito.

Tenere presente che i siti Web di Azure forniscono un metodo di routing del traffico failover per i siti Web che si trovano in un unico data center (noto anche come area), indipendentemente dalla modalità del sito Web. Gestione traffico consente di specificare il metodo di routing del traffico failover per i siti web che si trovano in data center diversi.

## Per configurare un metodo di routing del traffico failover:

1. Nel riquadro sinistro del portale di Azure fare clic sull'icona **Gestione traffico** per aprire il relativo riquadro. Se non è ancora stato creato il profilo di Gestione traffico, vedere [Gestire i profili di Gestione traffico](traffic-manager-manage-profiles.md) per i passaggi necessari per la creazione di un profilo di base di Gestione traffico.
2. Nel riquadro di Gestione traffico nel portale di Azure individuare il profilo di Gestione traffico in cui sono contenute le impostazioni da modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
3. Nella parte superiore della pagina del profilo fare clic su **Endpoint** e verificare che siano presenti i servizi cloud e i siti Web (endpoint) che si vuole includere nella configurazione. Per i passaggi necessari per aggiungere o rimuovere endpoint, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).
4. Nella parte superiore della pagina del profilo fare clic su **Configura** per aprire la pagina di configurazione.
5. Per **Impostazioni del metodo di routing del traffico**, verificare che il metodo di bilanciamento del carico sia **Failover**. In caso contrario, fare clic su **Failover** nell'elenco a discesa.
6. Per **Elenco priorità failover** regolare l'ordine di failover per gli endpoint. Quando si seleziona il metodo di routing del traffico **Failover**, l'ordine degli endpoint selezionati è rilevante. L'endpoint primario si trova nella parte superiore. Usare i tasti freccia SU e GIÙ per modificare l'ordine secondo le esigenze. Per informazioni su come impostare le priorità di failover usando Windows PowerShell, vedere [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verificare che le **Impostazioni di monitoraggio** siano configurate correttamente. Tramite il monitoraggio viene assicurato il mancato invio di traffico agli endpoint offline. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Si noti che la barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita). Per altre informazioni sul monitoraggio, vedere [Monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
8. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
9. Verificare le modifiche apportate alla configurazione. Per altre informazioni, vedere [Test delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md).
10. Dopo avere impostato e verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico. Per altre informazioni, vedere [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).

## Passaggi successivi

[Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md)

[Metodi di routing di Gestione traffico](traffic-manager-load-balancing-methods.md)

[Configurare il metodo di routing Round Robin](traffic-manager-configure-round-robin-routing-method.md)

[Configurare un metodo di routing del traffico delle prestazioni](traffic-manager-configure-performance-routing-method.md)

[Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)

[Gestione traffico: disabilitare, abilitare o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Gestione traffico: disabilitare o abilitare un endpoint](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_1203_2015-->