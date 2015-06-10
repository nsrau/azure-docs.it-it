<properties 
    pageTitle="Limiti del servizio RemoteApp"
    description="Informazioni sui limiti e i valori predefiniti per Azure RemoteApp" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/07/2015" 
    ms.author="elizapo" />


# Limiti e valori predefiniti del servizio RemoteApp

Questo argomento descrive i limiti del servizio per Azure RemoteApp.

  
## Limiti per le raccolte
Tutte le raccolte:

- Raccolte per utente: 1
- App pubblicate per raccolta: 5000 

Raccolte della versione di valutazione:

- Durata della versione di valutazione: 30 giorni
- Raccolte della versione di valutazione: 2 per sottoscrizione
- Utenti per raccolta della versione di valutazione: 10 
- Immagini modello della versione di valutazione: 25
 
Raccolte attivate (versione a pagamento):

- Raccolte della versione a pagamento: 3 (è possibile richiedere un aumento di questo limite)
- Immagini modello della versione a pagamento: 25

 
## Limiti per gli utenti

I limiti documentati delle raccolte sono flessibili (ossia possono essere modificati). Il numero massimo di utenti per ogni raccolta è un limite fisso e non può essere modificato. Tuttavia, il numero di utenti consentiti è determinato dal livello della raccolta, come indicato di seguito:


- Basic: 800 utenti
- Standard: 500 utenti

Il numero di utenti che è possibile supportare è determinato dal numero di macchine virtuali usate per la raccolta. Il livello Basic prevede 16 utenti per macchina virtuale, mentre il livello Standard prevede 10 utenti per macchina virtuale.

Per determinare il numero massimo di utenti assegnati che è possibile supportare, moltiplicare il numero di utenti a pagamento di ogni raccolta per il numero di raccolte a disposizione.
  
È possibile creare più raccolte nell'account con questi limiti e arrivare fino a 5000 connessioni utente simultanee in tutte le raccolte. Se è necessario un numero più alto, è possibile richiedere un'estensione dei limiti.

## Altri valori e limiti:

- Archiviazione dei dati utente per utente per raccolta: 50 GB
- Timeout di inattività: 4 ore
- Timeout di disconnessione: 4 ore

## Come richiedere un'estensione dei limiti
È possibile richiedere un'estensione per i limiti della raccolta e per gli utenti simultanei. A tale scopo, aprire un ticket di supporto spiegando le proprie esigenze.


Per aprire un ticket di supporto, seguire questa procedura:

1.	Nel portale di gestione fare clic su [Supporto](https://manage.windowsazure.com/?getsupport=true). Se non si è ancora connessi, verrà richiesto di immettere le credenziali.
2.	Selezionare la propria sottoscrizione.
3.	Come tipo di supporto selezionare **Tecnico**.
4.	Fare clic su **Crea ticket**. 
5.	Selezionare **Azure RemoteApp** dall'elenco dei prodotti visualizzato nella pagina successiva.
6.	Selezionare un **tipo di problema** appropriato per la situazione specifica.
7.	Fare clic su **Continua**.
8.	Seguire le istruzioni nella pagina successiva e quindi immettere i dettagli relativi all'estensione richiesta. Ad esempio, se si desidera estendere il periodo di valutazione, specificare questa richiesta indicando il numero di giorni. 
9.	Fare clic su **Invia** per aprire il ticket.

<!---HONumber=58-->