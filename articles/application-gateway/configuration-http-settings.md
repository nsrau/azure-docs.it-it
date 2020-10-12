---
title: Configurazione delle impostazioni HTTP del gateway applicazione Azure
description: Questo articolo descrive come configurare le impostazioni HTTP del gateway applicazione Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652882"
---
# <a name="application-gateway-http-settings-configuration"></a>Configurazione delle impostazioni HTTP del gateway applicazione

Il gateway applicazione instrada il traffico verso i server back-end usando la configurazione specificata qui. Dopo aver creato un'impostazione HTTP, è necessario associarla a una o più regole di routing delle richieste.

## <a name="cookie-based-affinity"></a>Affinità basata sui cookie

Applicazione Azure gateway usa i cookie gestiti dal gateway per la gestione delle sessioni utente. Quando un utente invia la prima richiesta al gateway applicazione, imposta un cookie di affinità nella risposta con un valore hash che contiene i dettagli della sessione, in modo che le richieste successive che trasportano il cookie di affinità vengano indirizzate allo stesso server back-end per mantenere la viscosità. 

Questa funzionalità è utile quando si desidera conservare una sessione utente sullo stesso server e quando lo stato della sessione viene salvato localmente nel server per una sessione utente. Se l'applicazione non è in grado di gestire l'affinità basata su cookie, non è possibile usare questa funzionalità. Per usarlo, assicurarsi che i client supportino i cookie.

L' [aggiornamento V80](https://chromiumdash.appspot.com/schedule) del [browser Chromium](https://www.chromium.org/Home) ha introdotto un mandato in cui i cookie HTTP senza attributo [navigava sullostesso sito](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) devono essere considerati navigava sullostesso sito = LAX. Se il cookie deve essere inviato in un contesto di terze parti, nel caso di richieste di condivisione risorse tra le origini (CORS), deve usare *navigava sullostesso sito = None; Proteggere* gli attributi e deve essere inviati solo tramite HTTPS. In caso contrario, in uno scenario solo HTTP, il browser non invia i cookie nel contesto di terze parti. L'obiettivo di questo aggiornamento di Chrome è quello di migliorare la sicurezza ed evitare attacchi di richiesta intersito falsa (CSRF). 

Per supportare questa modifica, a partire dal 17 2020 febbraio, il gateway applicazione (tutti i tipi di SKU) inserirà un altro cookie denominato *ApplicationGatewayAffinityCORS* oltre al cookie *ApplicationGatewayAffinity* esistente. Al cookie *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi (*"navigava sullostesso sito = None; Secure "*) in modo che la sessione appiccicosa venga mantenuta anche per le richieste tra origini.

Si noti che il nome del cookie di affinità predefinito è *ApplicationGatewayAffinity* ed è possibile modificarlo. Se si sta usando un nome di cookie di affinità personalizzato, viene aggiunto un cookie aggiuntivo con CORS come suffisso. Ad esempio, *CustomCookieNameCORS*.

> [!NOTE]
> Se l'attributo *navigava sullostesso sito = None* è impostato, è obbligatorio che il cookie contenga anche il flag *Secure* e che sia necessario inviarlo tramite HTTPS.  Se è necessaria l'affinità di sessione su CORS, è necessario eseguire la migrazione del carico di lavoro a HTTPS. Per un gateway applicazione, vedere la [Panoramica sull'](ssl-overview.md)offload TLS e la documentazione TLS end-to-end, [configurare un gateway applicazione con terminazione tls usando il portale di Azure](create-ssl-portal.md), [configurare TLS end-to-end usando il gateway applicazione con il portale](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Esaurimento delle connessioni

Lo svuotamento delle connessioni consente di rimuovere normalmente i membri del pool back-end durante gli aggiornamenti pianificati dei servizi. È possibile applicare questa impostazione a tutti i membri di un pool back-end abilitando lo svuotamento della connessione sull'impostazione HTTP. Garantisce che tutte le istanze di annullamento della registrazione di un pool back-end continuino a mantenere le connessioni esistenti e a gestire richieste in corso per un timeout configurabile e non ricevano nuove richieste o connessioni. L'unica eccezione è costituita dalle richieste associate per la deregistrazione delle istanze a causa dell'affinità di sessione gestita dal gateway e continueranno a essere inviate alle istanze di annullamento della registrazione. Lo svuotamento della connessione si applica alle istanze back-end che vengono rimosse in modo esplicito dal pool back-end.

## <a name="protocol"></a>Protocollo

Il gateway applicazione supporta sia HTTP che HTTPS per il routing delle richieste ai server back-end. Se si sceglie HTTP, il traffico verso i server back-end non è crittografato. Se la comunicazione non crittografata non è accettabile, scegliere HTTPS.

Questa impostazione combinata con HTTPS nel listener supporta [TLS end-to-end](ssl-overview.md). Ciò consente di trasmettere in modo sicuro dati sensibili crittografati al back-end. Ogni server back-end nel pool back-end con TLS abilitato end-to-end deve essere configurato con un certificato per consentire la comunicazione protetta.

## <a name="port"></a>Porta

Questa impostazione specifica la porta in cui i server back-end ascoltano il traffico dal gateway applicazione. È possibile configurare porte comprese tra 1 e 65535.

## <a name="request-timeout"></a>Timeout richiesta

Questa impostazione indica il numero di secondi di attesa del gateway applicazione per ricevere una risposta dal server back-end.

## <a name="override-back-end-path"></a>Esegui override del percorso back-end

Questa impostazione consente di configurare un percorso di invio personalizzato facoltativo da usare quando la richiesta viene trasmessa al back-end. Qualsiasi parte del percorso in ingresso che corrisponde al percorso personalizzato nel campo **percorso back-end di sostituzione** viene copiata nel percorso inviato. La tabella seguente illustra il funzionamento di questa funzionalità:

- Quando l'impostazione HTTP è associata a una regola di routing richiesta di base:

  | Richiesta originale  | Esegui override del percorso back-end | Richiesta da inviare al back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override.            | /override/home/              |
  | /home/secondhome/ | /override.            | /override/home/secondhome/   |

- Quando l'impostazione HTTP è associata a una regola di routing delle richieste basata sul percorso:

  | Richiesta originale           | Regola percorso       | Esegui override del percorso back-end | Richiesta da inviare al back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | regola      | /override.            | /override/home/              |
  | /pathrule/home/secondhome/ | regola      | /override.            | /override/home/secondhome/   |
  | /Home/                     | regola      | /override.            | /override/home/              |
  | /home/secondhome/          | regola      | /override.            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override.            | /override.                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override.            | /override/secondhome/        |
  | regola                 | regola      | /override.            | /override.                   |

## <a name="use-for-app-service"></a>Usare per il servizio app

Si tratta di un collegamento solo interfaccia utente che seleziona le due impostazioni necessarie per il back-end del servizio app Azure. Consente **di selezionare il nome host dall'indirizzo back-end**e crea un nuovo probe personalizzato se non ne è già presente uno. Per ulteriori informazioni, vedere la sezione [pick host name from back-end Address](#pick-host-name-from-back-end-address)setting di questo articolo. Viene creato un nuovo probe e l'intestazione del probe viene prelevata dall'indirizzo del membro back-end.

## <a name="use-custom-probe"></a>USA Probe personalizzato

Questa impostazione associa un [Probe personalizzato](application-gateway-probe-overview.md#custom-health-probe) a un'impostazione http. È possibile associare un solo Probe personalizzato a un'impostazione HTTP. Se non si associa in modo esplicito un probe personalizzato, viene usato il [Probe predefinito](application-gateway-probe-overview.md#default-health-probe-settings) per monitorare l'integrità del back-end. Si consiglia di creare un probe personalizzato per un maggiore controllo sul monitoraggio dello stato dei back-end.

> [!NOTE]
> Il probe personalizzato non monitora l'integrità del pool back-end, a meno che l'impostazione HTTP corrispondente non sia associata in modo esplicito a un listener.

## <a name="pick-host-name-from-back-end-address"></a>Selezionare il nome host dall'indirizzo back-end

Questa funzionalità imposta dinamicamente l'intestazione *host* nella richiesta sul nome host del pool back-end. Usa un indirizzo IP o un FQDN.

Questa funzionalità aiuta quando il nome di dominio del back-end è diverso dal nome DNS del gateway applicazione e il back-end si basa su un'intestazione host specifica per la risoluzione nell'endpoint corretto.

Un caso di esempio è un servizio multi-tenant come back-end. Un servizio app è un servizio multi-tenant che usa uno spazio condiviso con un singolo indirizzo IP. È quindi possibile accedere a un servizio app solo tramite i nomi host configurati nelle impostazioni del dominio personalizzato.

Per impostazione predefinita, il nome di dominio personalizzato è *example.azurewebsites.NET*. Per accedere al servizio app usando un gateway applicazione tramite un nome host non registrato in modo esplicito nel servizio app o tramite il nome di dominio completo del gateway applicazione, è necessario sostituire il nome host nella richiesta originale con il nome host del servizio app. A tale scopo, abilitare l'impostazione del **nome host pick dall'indirizzo back-end** .

Per un dominio personalizzato il cui nome DNS personalizzato esistente è mappato al servizio app, non è necessario abilitare questa impostazione.

> [!NOTE]
> Questa impostazione non è necessaria per ambiente del servizio app, ovvero una distribuzione dedicata.

## <a name="host-name-override"></a>Override del nome host

Questa funzionalità sostituisce l'intestazione *host* nella richiesta in ingresso nel gateway applicazione con il nome host specificato.

Se, ad esempio, si specifica *www.contoso.com* nell'impostazione **nome host** , la richiesta originale * `https://appgw.eastus.cloudapp.azure.com/path1` viene modificata in * `https://www.contoso.com/path1` quando la richiesta viene trasmessa al server back-end.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sul pool back-end](configuration-overview.md#back-end-pool)