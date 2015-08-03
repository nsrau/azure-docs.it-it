<properties 
	pageTitle="Uso di castLabs per distribuire licenze DRM a Servizi multimediali di Azure" 
	description="Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze castLabs." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="juliako"/>


#Uso di castLabs per distribuire licenze DRM a Servizi multimediali di Azure

##Panoramica

Questo articolo illustra come usare Servizi multimediali di Azure per distribuire un flusso crittografato in modo dinamico da Servizi multimediali di Azure mediante DRM di PlayReady e Widevine. La licenza per PlayReady viene distribuita dal server licenze PlayReady di Servizi multimediali, mentre la licenza per Widevine viene distribuita dal server licenze **castLabs**.

Il diagramma seguente illustra un'architettura di integrazione di alto livello tra castLabs e Servizi multimediali di Azure.

![integrazione](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##Configurazione di sistema tipica

- I contenuti multimediali vengono archiviati in Servizi multimediali di Azure.
- Gli ID delle chiavi simmetriche vengono archiviati sia in castLabs sia in Servizi multimediali di Azure.
- castLabs e Servizi multimediali di Azure dispongono entrambi di un sistema di autenticazione dei token integrato. I token di autenticazione vengono illustrati nelle sezioni seguenti. 
- Quando un client deve trasmettere un video in streaming, il contenuto viene crittografato dinamicamente con la **crittografia comune** e organizzato da Servizi multimediali di Azure in pacchetti dinamici creati con i protocolli Smooth Streaming e DASH. Viene inoltre fornita la crittografia dei flussi elementari M2TS PlayReady per il protocollo di streaming HLS.
- La licenza per PlayReady viene recuperata dal server licenze di Servizi multimediali di Azure, mentre la licenza per Widevine viene recuperata dal server licenze castLabs. 
- Media Player decide automaticamente le licenze da recuperare in base alle caratteristiche della piattaforma client. 

##Generazione di token di autenticazione per ottenere una licenza

castLabs e Servizi multimediali di Azure supportano entrambi il formato di token JWT (JSON Web Token), necessario per autorizzare una licenza.

###Token JWT in Servizi multimediali di Azure 

La tabella seguente descrive il token JWT usato in Servizi multimediali di Azure.

<table border="1">
<tr><td>Issuer</td><td>Stringa dell'autorità di certificazione rilasciata dal servizio token di sicurezza scelto</td></tr>
<tr><td>Audience</td><td>Stringa dei destinatari rilasciata dal servizio token di sicurezza usato</td></tr>
<tr><td>Claims</td><td>Set di attestazioni</td></tr>
<tr><td>NotBefore</td><td>Validità di inizio del token</td></tr>
<tr><td>Expires</td><td>Validità di fine del token</td></tr>
<tr><td>SigningCredentials</td><td>Chiave condivisa tra il server licenze PlayReady, il server licenze castLabs e il servizio token di sicurezza (STS); può essere una chiave simmetrica o asimmetrica.</td></tr>
</table>

###Token JWT in castLabs

La tabella seguente descrive il token JWT usato in castLabs.

<table border="1">
<tr><td>optData</td><td>Stringa JSON contenente informazioni relative all'utente. </td></tr>
<tr><td>crt</td><td>Stringa JSON contenente informazioni sull'asset, sulla relativa licenza e sui diritti di riproduzione.</td></tr>
<tr><td>iat</td><td>Data e ora corrente nel periodo.</td></tr>
<tr><td>jti</td><td>Identificatore univoco per il token (ogni token può essere usato una sola volta nel sistema castLabs).</td></tr>
</table>

##Configurazione della soluzione di esempio 

La [soluzione di esempio](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) è costituita da due progetti:

-	Un'app console che consente di impostare le restrizioni DRM su un asset già acquisito, sia per PlayReady sia per Widevine.
-	Un'applicazione Web incaricata della distribuzione dei token, che possono essere considerati come una versione molto semplificata di un servizio token di sicurezza.


Per usare l'applicazione console:

1.	Modificare il file app.config per impostare le credenziali di Servizi multimediali di Azure, le credenziali di castLabs, la configurazione del servizio token di sicurezza e la chiave condivisa.
2.	Caricare un asset in Servizi multimediali di Azure.
3.	Ottenere l'UUID dall'asset caricato e modificare la riga 32 nel file Program.cs:

		 var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.	Usare un ID asset per assegnare un nome all'asset nel sistema castLabs (riga 44 nel file Program.cs).

	È necessario impostare l'ID asset per **castLabs**: deve essere una stringa alfanumerica univoca.

5.	Eseguire il programma.


Per usare l'applicazione Web (STS):

1.	Modificare il file web.config per impostare l'ID società di castlabs, la configurazione del servizio token di sicurezza e la chiave condivisa.
2.	Eseguire la distribuzione nei siti Web di Azure.
3.	Passare al sito Web.

##Riproduzione di un video

Per riprodurre un video crittografato con la crittografia comune (PlayReady), è possibile usare [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Quando si esegue l'app console, vengono restituiti l'ID della chiave simmetrica e l'URL del manifesto.

1.	Aprire una nuova scheda e avviare il servizio token di sicurezza: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.	Accedere a [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.	Incollare l'URL di streaming
4.	Scegliere la casella di controllo **Opzioni avanzate**.
5.	Selezionare PlayReady nell'elenco a discesa **Protezione**.
6.	Incollare il token ottenuto dal servizio token di sicurezza nella casella di testo Token.
7.	Aggiornare il lettore.
8.	A questo punto, dovrebbe essere possibile riprodurre il video.

Per riprodurre il video protetto in HTML5 con Chrome tramite il lettore castLabs, contattare yanmf@microsoft.com per ottenere l'accesso al lettore. Nel momento in cui si ottiene l'accesso, è necessario tenere presente due aspetti:

1.	Il lettore castLabs deve poter accedere al file manifesto MPEG-DASH. Aggiungere quindi (format=mpd-time-csf) al proprio file manifesto per ottenere il file manifesto MPEG-DASH, anziché il file Smooth Streaming predefinito.

2.	Quando si usa il server licenze castLabs, non è necessario aggiungere il prefisso "Bearer=" davanti al token. Se presente, quindi, è necessario rimuoverlo prima di inviare il token.

 

<!---HONumber=July15_HO4-->