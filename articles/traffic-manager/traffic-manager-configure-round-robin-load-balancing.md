<properties
   pageTitle="Configurare il bilanciamento del carico round robin di Gestione traffico"
   description="Informazioni su come configurare il bilanciamento del carico round robin per gli endpoint di Gestione traffico."
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
   ms.date="05/27/2015"
   ms.author="joaoma;cherylmc" />

# Configurare il bilanciamento del carico round robin

Un modello comune di bilanciamento del carico consiste nel fornire un set di endpoint identici, che includono servizi cloud e siti Web, e nell'inviare traffico a ciascuno di essi in base a uno schema round robin. Nei passaggi seguenti viene descritto come configurare Gestione traffico per eseguire questo tipo di bilanciamento del carico. Per altre informazioni sui diversi metodi di bilanciamento del carico, vedere [Informazioni sui metodi di bilanciamento del carico di Gestione traffico](traffic-manager-load-balancing-methods.md).

>[AZURE.NOTE]Siti Web di Azure offre già funzionalità di bilanciamento del carico round robin per i siti Web che si trovano all'interno di un data center (nota anche come area). Gestione traffico consente di specificare il bilanciamento del carico round robin per i siti Web che si trovano in data center diversi.

## Bilanciare il carico del traffico in modo equo (round robin) in un set di endpoint

1. Nel riquadro sinistro del portale di gestione fare clic sull'icona **Gestione traffico** per aprire il relativo riquadro. Se non è ancora stato creato il profilo di Gestione traffico, vedere [Gestire i profili di Gestione traffico](traffic-manager-manage-profiles.md) per i passaggi necessari per la creazione di un profilo di base di Gestione traffico.
2. Nel riquadro di Gestione traffico nel portale di gestione individuare il profilo di Gestione traffico in cui sono contenute le impostazioni che si desidera modificare, quindi fare clic sulla freccia a destra del nome del profilo. Verrà aperta la pagina delle impostazioni per il profilo.
3. Nella parte superiore della pagina del profilo fare clic su **Endpoint** e verificare che siano presenti gli endpoint servizio che si desidera includere nella configurazione. Per i passaggi necessari per aggiungere o rimuovere endpoint, vedere [Gestire gli endpoint in Gestione traffico](traffic-manager-endpoints.md).
4. Nella parte superiore della pagina del profilo fare clic su **Configura** per aprire la pagina di configurazione.
5. Per **Impostazioni del metodo di bilanciamento del carico**, verificare che il metodo di bilanciamento del carico sia **Round robin**. In caso contrario, fare clic su **Round robin** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** siano configurate correttamente. Tramite il monitoraggio viene assicurato il mancato invio di traffico agli endpoint offline. Per monitorare gli endpoint, è necessario specificare un percorso e un nome file. Si noti che la barra ("/") è una voce valida per il percorso relativo e implica che il file si trovi nella directory radice (impostazione predefinita). Per altre informazioni sul monitoraggio, vedere [Informazioni sul monitoraggio di Gestione traffico](../traffic-manager-about-monitoring.md).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche apportate alla configurazione. Per altre informazioni, vedere [Test delle impostazioni di Gestione traffico](traffic-manager-testing-settings.md).
9. Dopo avere impostato e verificato il funzionamento del profilo di Gestione traffico, modificare il record DNS sul server DNS autorevole per fare in modo che il nome del dominio aziendale punti al nome di dominio di Gestione traffico. Per altre informazioni, vedere [Impostare un dominio Internet aziendale in modo che punti a un dominio di Gestione traffico](traffic-manager-point-internet-domain.md).

## Vedere anche

[Informazioni sui metodi di bilanciamento del carico di Gestione traffico](traffic-manager-load-balancing-methods.md)

[Attività di configurazione di Gestione traffico](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Panoramica di Gestione traffico](../traffic-manmager-overview.md)

[Servizi cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Operazioni per Gestione traffico (informazioni di riferimento API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di Gestione traffico di Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=August15_HO6-->