<properties
   pageTitle="Abilitazione di criteri SSL ed SSL end-to-end nel gateway applicazione | Microsoft Azure"
   description="Questa pagina offre un'introduzione al supporto di SSL end-to-end del gateway applicazione."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amsriva"/>


# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Abilitazione di criteri SSL ed SSL end-to-end nel gateway applicazione

## <a name="overview"></a>Panoramica

Il gateway applicazione supporta la terminazione SSL nel gateway, dopo la quale il traffico scorre generalmente non crittografato verso i server back-end. In questo modo, i server Web non devono gestire il costoso carico di crittografia/decrittografia. Tuttavia, per alcuni clienti le comunicazioni non crittografate verso i server back-end non rappresentano un'opzione accettabile. Questo potrebbe dipendere da requisiti di sicurezza/conformità o da un'applicazione in grado di accettare solo connessioni protette. Per queste applicazioni, il gateway applicazione supporta ora la crittografia SSL end-to-end.

La crittografia SSL end-to-end consente di trasmettere in modo sicuro i dati sensibili nel back-end crittografato, sfruttando i vantaggi delle funzioni di bilanciamento del carico di Livello 7 fornite dal gateway applicazione, come ad esempio l'affinità basata su cookie, il routing basato sull'URL, il supporto per il routing basato su siti o la possibilità di inserire intestazioni in formato X-Forwarded-*.

Se configurato con la modalità di comunicazione SSL end-to-end, il gateway applicazione termina le sessioni SSL dell'utente nel gateway ed esegue la decrittografia del traffico utente. Applica quindi le regole configurate per selezionare un'istanza del pool di back-end adeguata su cui instradare il traffico. Il gateway applicazione avvia a questo punto una nuova connessione SSL al server back-end e crittografa nuovamente i dati tramite il certificato di chiave pubblica del server back-end prima di trasmettere la richiesta al back-end. L'SSL end-to-end viene abilitato impostando la configurazione del protocollo in BackendHTTPSetting su Https, che viene quindi applicata a un pool di back-end. Per una comunicazione protetta, ogni server back-end nel pool di back-end con SSL end-to-end abilitato deve essere configurato con un certificato.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

In questo esempio, le richieste di https://contoso.com possono essere instradate a ContosoServerPool su HTTP e https://fabrikam.com verrà indirizzato a FabrikamServerPool su HTTPS tramite SSL end-to-end.

## <a name="end-to-end-ssl-and-white-listing-of-certificates"></a>SSL end-to-end e aggiunta dei certificati all'elenco dei consentiti

Il gateway applicazione comunica solo con istanze di back-end note, che hanno inserito i relativi certificati nell'elenco dei consentiti del gateway applicazione. Per abilitare l'aggiunta dei certificati all'elenco dei consentiti, è necessario caricare la chiave pubblica dei certificati del server back-end nel gateway applicazione. Saranno consentite solo connessioni a back-end noti o aggiunti all'elenco dei consentiti. Le altre connessioni generano un errore del gateway. I certificati autofirmati vengono usati a scopo di test e non sono consigliati per i carichi di lavoro. Prima dell'uso, anche questi certificati devono essere aggiunti all'elenco dei consentiti nel gateway applicazione, come descritto in precedenza.

## <a name="application-gateway-ssl-policy"></a>Criteri SSL del gateway applicazione

Il gateway applicazione supporta anche criteri di negoziazione SSL configurabili dell'utente, che consentono ai clienti di avere un controllo più granulare sulle connessioni SSL nel gateway applicazione.

1. SSL 2.0 e 3.0 sono disabilitati per tutti i gateway applicazione. Non sono configurabili in nessun modo.
2. La definizione dei criteri SSL consente all'utente di scegliere se disabilitare i 3 protocolli seguenti: TLSv1_0, TLSv1_1 e TLSv1_2.
3. Se non è definito alcun criterio SSL, sono abilitati tutti e tre (TLSv1_0, TLSv1_1 e TLSv1_2).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso i concetti di SSL end-to end e i criteri SSL, passare all'articolo che spiega come [abilitare SSL end-to-end nel gateway applicazione](application-gateway-end-to-end-ssl-powershell.md) per creare un gateway applicazione che consenta di inviare traffico al back-end in formato crittografato.



<!--HONumber=Oct16_HO2-->


