<properties 
   pageTitle="Uso del connettore Box nell'app per la logica" 
   description="Uso del connettore Box nell'app per la logica" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# **Uso del connettore Box nell'app per la logica**

 

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. In alcuni scenari potrebbe essere necessario usare un connettore Box, che consente di condividere i dati in modo sicuro con chiunque, anche con chi è all'esterno del firewall.

 

L'app della raccolta Box fornisce il trigger e le azioni come meccanismi per interagire con Box:

 

1. **Attiva** **se è disponibile un file**: da usare se si vuole avviare un'app per la logica quando un file viene aggiunto a una cartella Box. Il trigger controlla la cartella Box specificata in base a una frequenza configurata e attiva il flusso quando c'è un file disponibile nella cartella specificata. Restituisce i contenuti e le proprietà del file e, dopo averli passati al successivo passaggio dell'app per la logica, il file viene eliminato.


	<table>
  <tr>
    <td><b>Nome parametro</b></td>
    <td><b>Descrizione</b></td>
    <td><b>Obbligatorio</b></td>
  </tr>
  <tr>
    <td>Percorso file</td>
    <td>Percorso della cartella in cui è presente il file.</td>
    <td>Sì</td>
  </tr>
  <tr>
    <td>Tipo file</td>
    <td>Specifica se il file è di testo o binario.</td>
    <td>No</td>
  </tr>
  <tr>
    <td>Frequenza</td>
    <td>Specifica il tipo di frequenza. È necessario selezionarne uno dai tipi elencati. Può essere uno dei seguenti: Anni, Mesi, Settimane, Giorni, Ore, Minuti o Secondi.</td>
    <td>Sì</td>
  </tr>
  <tr>
    <td>Interval</td>
    <td>Specifica l'unità per la frequenza.</td>
    <td>Sì</td>
  </tr>
</table>


 

2. **Azioni**: le azioni consentono di eseguire azioni predefinite sull'account Box configurato con l'app per la logica. Di seguito sono riportate le azioni che è possibile eseguire sull'account Box con il connettore Box:

	a. *Elenca file:* questa operazione restituirà le informazioni di tutti i file di una cartella. Di seguito è riportato l'elenco dei parametri obbligatori per l'azione:

	<table>
  <tr>
    <td><b>Nome parametro</b></td>
    <td><b>Descrizione</b></td>
    <td><b>Obbligatorio</b></td>
  </tr>
  <tr>
    <td>Percorso cartella</td>
    <td>Percorso della cartella in cui deve essere eseguita l'inclusione in elenco.</td>
    <td>Sì</td>
  </tr>
</table>*Nota: non restituisce i contenuti dei file.*

 

    b. *Recupera file:* questa operazione recupera un file, inclusi il contenuto e le proprietà. Di seguito è riportato l'elenco dei parametri obbligatori per l'azione:

	<table>
  <tr>
    <td><b>Nome parametro</b></td>
    <td><b>Descrizione</b></td>
    <td><b>Obbligatorio</b></td>
  </tr>
  <tr>
    <td>Percorso file</td>
    <td>Percorso della cartella in cui è presente il file.</td>
    <td>Sì</td>
  </tr>
  <tr>
    <td>Tipo file</td>
    <td>Specifica se il file è di testo o binario.</td>
    <td>No</td>
  </tr>
</table>*Nota: questa operazione non eliminerà il file dopo averlo letto.*

 

    c. Carica file: come indicato dal nome, l'azione carica il file nell'account Box. Se il file esiste già, non viene sovrascritto e viene generato un errore. Di seguito è riportato l'elenco dei parametri obbligatori per l'azione:

	<table>
  <tr>
    <td><b>Nome parametro</b></td>
    <td><b>Descrizione</b></td>
    <td><b>Obbligatorio</b></td>
  </tr>
  <tr>
    <td>Percorso file</td>
    <td>Percorso del file.</td>
    <td>Sì</td>
  </tr>
  <tr>
    <td>Contenuto file</td>
    <td>Contenuto del file da caricare.</td>
    <td>Sì</td>
  </tr>
  <tr>
    <td>Codifica trasferimento contenuto</td>
    <td>Tipo di codifica del contenuto, può essere Base64 o None.</td>
    <td> </td>
  </tr>
</table>d. Elimina file: l'azione elimina il file specificato da una cartella. Se non è possibile trovare il file o la cartella, viene generata un'eccezione. Di seguito è riportato l'elenco dei parametri obbligatori per l'azione:

 	<table>
  <tr>
    <td><b>Nome parametro</b></td>
    <td><b>Descrizione</b></td>
    <td><b>Obbligatorio</b></td>
  </tr>
  <tr>
    <td>Percorso file</td>
    <td>Percorso della cartella in cui è presente il file.</td>
    <td>Sì</td>
  </tr>
</table>


 

## **Creazione di un connettore Box per l'app per la logica** ##

Per usare il connettore Box, prima è necessario creare un'istanza dell'app per le API Box Connector. Attenersi alla procedura riportata di seguito:

1. Aprire Azure Marketplace con l'opzione + NUOVO in basso a destra nel portale di Azure.

2. Andare a "Web e dispositivi mobili > App per le API" e cercare "Box Connector".

3. Configurare il connettore Box e fare clic su Crea:

	![][1]

4. A questo punto è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore Box.


## **Uso del connettore Box nell'app per la logica** ##

Una volta creata l'app per le API, è possibile usare il connettore Box come trigger o come azione per l'app per la logica. A questo scopo, è necessario:


1. Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore Box.

2. Aprire "Trigger e azioni" per aprire la finestra di progettazione delle app per la logica e configurare il flusso. Il connettore Box apparirà nella sezione "Usati di recente" nella raccolta sulla destra. Selezionarlo.

3. Se il connettore Box viene selezionato all'avvio dell'app per la logica, agisce come trigger. In caso contrario, è possibile eseguire azioni sull'account Box con il connettore.

4. Il primo passaggio prevede l'autenticazione e l'autorizzazione delle app per la logica in modo che eseguano operazioni per conto dell'utente. Per avviare l'autorizzazione, fare clic su Autorizza in Box Connector.

	![][2]

5. Facendo clic su Autorizza, si aprirà la finestra di dialogo di autenticazione di Box. Specificare i dettagli di accesso dell'account Box su cui si vuole eseguire le operazioni.

	![][3]

6. Concedere alle app per la logica l'accesso all'account utente per eseguire l'operazione per conto dell'utente.

	![][4]

7. Se il connettore Box viene configurato come trigger, vengono mostrati i trigger. In caso contrario, viene visualizzato l'elenco delle azioni ed è possibile scegliere l'operazione appropriata da eseguire.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg


<!--HONumber=54-->