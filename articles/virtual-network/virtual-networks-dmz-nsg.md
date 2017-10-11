---
title: 'Esempio di rete perimetrale di Azure: Creare una rete perimetrale semplice con gruppi di sicurezza di rete | Microsoft Docs'
description: Creare una rete perimetrale con gruppi di sicurezza di rete
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: ec29e6b250f927a3a4a94ffdf83d6c7c0e325722
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="example-1--build-a-simple-dmz-using-nsgs-with-an-azure-resource-manager-template"></a>Esempio 1: Creare una rete perimetrale semplice usando gruppi di sicurezza di rete con un modello di Azure Resource Manager
[Tornare alla pagina relativa alle procedure consigliate sui limiti di sicurezza][HOME]

> [!div class="op_single_selector"]
> * [Modello di Resource Manager](virtual-networks-dmz-nsg.md)
> * [Classica: PowerShell](virtual-networks-dmz-nsg-asm.md)
> 
>

Questo esempio descrive come creare una rete perimetrale primitiva con quattro server Windows e gruppi di sicurezza di rete. Per comprendere in modo approfondito ogni passaggio, questo esempio descrive tutte le sezioni del modello pertinenti. È disponibile anche una sezione sugli scenari di traffico con istruzioni dettagliate sul percorso seguito dal traffico attraverso i livelli di difesa della rete perimetrale. La sezione Riferimenti, infine, include le istruzioni e il codice del modello completi per creare l'ambiente per testare e sperimentare vari scenari. 

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 

![Rete perimetrale in ingresso con gruppo di sicurezza di rete][1]

## <a name="environment-description"></a>Descrizione dell'ambiente
Una sottoscrizione in questo esempio include le risorse seguenti:

* Un unico gruppo di risorse
* Una rete virtuale con due subnet: front-end e back-end
* Un gruppo di sicurezza di rete applicato a entrambe le subnet
* Un server Windows che rappresenta un server Web applicazioni ("IIS01")
* Due server Windows che rappresentano i server applicazioni back-end ("AppVM01", "AppVM02")
* Un server Windows che rappresenta un server DNS ("DNS01")
* Un indirizzo IP pubblico associato al server Web applicazioni

Un collegamento nella sezione Riferimenti a un modello di Azure Resource Manager compila l'ambiente descritto in questo esempio. La creazione di VM e reti virtuali, anche se eseguita dal modello di esempio, non è descritta in dettaglio in questo documento. 

**Per compilare questo ambiente**. Istruzioni dettagliate sono disponibili nella sezione Riferimenti di questo documento;

1. Distribuire il modello di Azure Resource Manager in [Modelli di avvio rapido di Azure][Template]
2. Installare l'applicazione di esempio in: [Script di applicazione di esempio][SampleApp]

>[!NOTE]
>Per eseguire la connessione tramite RDP ai server back-end in questa istanza, il server IIS viene usato come "jumpbox". Eseguire prima la connessione tramite RDP al server IIS e, quindi, dal server IIS eseguire la connessione tramite RDP al server back-end. In alternativa è possibile associare un indirizzo IP alla scheda di interfaccia di rete di ogni server per semplificare la connessione tramite RDP.
> 
>

Le sezioni seguenti descrivono in modo dettagliato il gruppo di sicurezza di rete e il relativo funzionamento per questo esempio spiegando le righe principali del modello di Azure Resource Manager.

## <a name="network-security-groups-nsg"></a>Gruppi di sicurezza di rete (NGS)
Per questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono caricate sei regole. 

>[!TIP]
>In genere, è consigliabile creare prima di tutto le regole specifiche di tipo "Consenti" e infine le regole di tipo "Nega" più generiche. La priorità assegnata determina quali regole vengono valutate per prime. Quando si rileva che al traffico è applicabile una determinata regola, non vengono valutate altre regole. Le regole del gruppo di sicurezza di rete possono essere applicate nella direzione in ingresso o in uscita, dal punto di vista della subnet.
>
>

A livello dichiarativo, per il traffico in ingresso vengono create le righe seguenti:

1. Il traffico DNS interno (porta 53) è consentito.
2. Il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale è consentito.
3. Il traffico HTTP (porta 80) da Internet al server Web (IIS01) è consentito.
4. Tutto il traffico (tutte le porte) da IIS01 ad AppVM1 è consentito.
5. Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) viene bloccato.
6. Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene bloccato.

Con queste regole associate a ogni subnet, se una richiesta HTTP proviene da Internet ed è diretta verso il server Web, le regole 3 (consenti) e 5 (nega) saranno applicabili, ma poiché la regola 3 ha una priorità maggiore, verrà applicata solo tale regola e la regola 5 non verrà presa in considerazione. La richiesta HTTP verrà quindi consentita sul server Web. Se lo stesso traffico prova a raggiungere il server DNS01, la regola 5 (nega) sarà la prima applicabile e il traffico non sarà autorizzato a passare al server. La regola 6 (nega) impedisce alla subnet front-end di comunicare con la subnet back-end, ad eccezione del traffico consentito nelle regole 1 e 4; questo set di regole protegge la rete back-end nel caso in cui un utente malintenzionato comprometta l'applicazione Web sul front-end. L'utente malintenzionato avrà infatti accesso limitato alla rete "protetta" back-end, ovvero solo alle risorse esposte nel server AppVM01.

Esiste una regola in uscita predefinita che consente il traffico in uscita verso Internet. Per questo esempio si consente il traffico in uscita e non si modificano le regole in uscita. Per applicare criteri di sicurezza al traffico in entrambe le direzioni, è obbligatorio il routing definito dall'utente, descritto nell'"Esempio 3" della [pagina relativa alle procedure consigliate sui limiti di sicurezza][HOME].

Ogni regola viene illustrata in dettaglio nel modo seguente:

1. È necessario creare l'istanza della risorsa gruppo di sicurezza di rete per inserire le regole:

    ```JSON
    "resources": [
      {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Network/networkSecurityGroups",
        "name": "[variables('NSGName')]",
        "location": "[resourceGroup().location]",
        "properties": { }
      }
    ]
    ``` 

2. La prima regola in questo esempio consente il traffico DNS fra tutte le reti interne al server DNS nella subnet back-end. Nella regola sono inclusi alcuni parametri importanti:
  * Questi tag sono identificatori forniti dal sistema che consentono di gestire in modo semplice una categoria più ampia di prefissi di indirizzi; "destinationAddressPrefix": le regole possono usare un tipo speciale di prefisso denominato "Tag predefinito". Questa regola usa il tag predefinito "Internet" per indicare qualsiasi indirizzo all'esterno della rete virtuale. Altre etichette di prefisso sono VirtualNetwork e AzureLoadBalancer.
  * "Direction" indica la direzione del flusso di traffico a cui verrà applicata questa regola dal punto di vista della subnet o della macchina virtuale, a seconda della posizione a cui è associato il gruppo di sicurezza di rete. Se Direction è impostato su "Inbound", la regola verrà applicata al traffico in ingresso nella subnet, ma non al traffico in uscita da essa.
  * "Priority" consente di impostare l'ordine in base al quale viene valutato un flusso di traffico. Più è basso il numero, maggiore sarà la priorità. Quando un flusso di traffico specifico è applicabile a una determinata regola, non vengono elaborate altre regole. Se quindi una regola con priorità 1 consente il traffico e una regola con priorità 2 lo blocca ed entrambe le regole sono applicabili, il passaggio del traffico viene consentito perché viene applicata la regola 1 con priorità più alta e non vengono considerate altre regole.
  * "Access" indica se il traffico a cui si applica la regola viene bloccato ("Deny") o consentito ("Allow").

    ```JSON
    "properties": {
    "securityRules": [
      {
        "name": "enable_dns_rule",
        "properties": {
          "description": "Enable Internal DNS",
          "protocol": "*",
          "sourcePortRange": "*",
          "destinationPortRange": "53",
          "sourceAddressPrefix": "VirtualNetwork",
          "destinationAddressPrefix": "10.0.2.4",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
    ```

3. Questa regola consente il flusso del traffico RDP da Internet alla porta RDP su qualsiasi server sulla subnet associata. 

    ```JSON
    {
      "name": "enable_rdp_rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 110,
        "direction": "Inbound"
      }
    },
    ```

4. Questa regola consente al traffico Internet in ingresso di raggiungere il server Web. Il comportamento di routing rimane invariato, ma il traffico destinato a IIS01 può transitare. Se quindi il traffico proveniente da Internet ha come destinazione il server Web, viene consentito e non vengono elaborate altre regole (nella regola con priorità 140 viene bloccato qualsiasi altro tipo di traffico Internet in ingresso). Se si elabora soltanto traffico HTTP, questa regola può essere limitata ulteriormente in modo da consentire esclusivamente la porta di destinazione 80.

    ```JSON
    {
      "name": "enable_web_rule",
      "properties": {
        "description": "Enable Internet to [variables('VM01Name')]",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "10.0.1.5",
        "access": "Allow",
        "priority": 120,
        "direction": "Inbound"
        }
      },
    ```

5. Questa regola consente il passaggio del traffico dal server IIS01 al server AppVM01 e una regola successiva blocca tutto il resto del traffico dal front-end al back-end. Per migliorare la regola, è consigliabile aggiungere la porta, se è nota. Ad esempio, se il server IIS ha necessità di raggiungere solo SQL Server in AppVM01, l'impostazione dell'intervallo delle porte di destinazione (DestinationPortRange) deve essere modificata da "*" (qualsiasi) a 1433 (porta SQL), in modo da esporre una superficie di attacco più limitata in AppVM01 nel caso l'applicazione Web venisse compromessa.

    ```JSON
    {
      "name": "enable_app_rule",
      "properties": {
        "description": "Enable [variables('VM01Name')] to [variables('VM02Name')]",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "10.0.1.5",
        "destinationAddressPrefix": "10.0.2.5",
        "access": "Allow",
        "priority": 130,
        "direction": "Inbound"
      }
    },
     ```

6. Questa regola blocca il traffico da Internet a qualsiasi server della rete. Insieme alla regola con priorità 110 e 120, consente esclusivamente il traffico Internet in ingresso diretto al firewall e alle porte RDP sui server e blocca tutto il traffico restante. Questa regola è di tipo fail-safe per bloccare tutti i flussi imprevisti.

    ```JSON
    {
      "name": "deny_internet_rule",
      "properties": {
        "description": "Isolate the [variables('VNetName')] VNet from the Internet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "VirtualNetwork",
        "access": "Deny",
        "priority": 140,
        "direction": "Inbound"
      }
    },
     ```

7. La regola finale blocca il traffico dalla subnet front-end alla subnet back-end. Poiché si tratta di una regola solo di tipo Inbound, il traffico in direzione opposta è consentito (dal back-end al front-end).

    ```JSON
    {
      "name": "deny_frontend_rule",
      "properties": {
        "description": "Isolate the [variables('Subnet1Name')] subnet from the [variables('Subnet2Name')] subnet",
        "protocol": "*",
        "sourcePortRange": "*",
        "destinationPortRange": "*",
        "sourceAddressPrefix": "[variables('Subnet1Prefix')]",
        "destinationAddressPrefix": "[variables('Subnet2Prefix')]",
        "access": "Deny",
        "priority": 150,
        "direction": "Inbound"
      }
    }
    ```

## <a name="traffic-scenarios"></a>Scenari di traffico
#### <a name="allowed-internet-to-web-server"></a>(*Consentito*) Da Internet al server Web
1. Un utente su Internet richiede una pagina HTTP dall'indirizzo IP pubblico della scheda di interfaccia di rete associata alla scheda di interfaccia di rete di IIS01
2. L'indirizzo IP pubblico passa il traffico alla rete virtuale verso IIS01 (il server Web)
3. La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1. Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2. Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3. Regola gruppo di sicurezza di rete 3 (da Internet a IIS01) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
4. Il traffico raggiunge l'indirizzo IP interno del server Web IIS01 (10.0.1.5).
5. IIS01 è in ascolto del traffico Web, riceve la richiesta e ne avvia l'elaborazione.
6. IIS01 chiede informazioni a SQL Server in AppVM01.
7. Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
8. La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1. Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2. Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3. Regola gruppo di sicurezza di rete 3 (da Internet a firewall), non applicabile, passa alla regola successiva.
  4. Regola gruppo di sicurezza di rete 4 (da IIS01 ad AppVM01) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
9. AppVM01 riceve la query SQL e risponde.
10. Non essendoci regole in uscita sulla subnet back-end, la risposta è consentita
11. La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1. Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2. La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
12. Il server IIS riceve la risposta SQL, completa la risposta HTTP e la invia al richiedente.
13. Non essendoci regole in uscita sulla subnet front-end, la risposta è consentita e l'utente su Internet riceve la pagina Web richiesta.

#### <a name="allowed-rdp-to-iis-server"></a>(*Consentito*) Traffico RDP al server IIS
1. L'amministratore del server su Internet richiede una sessione RDP a IIS01 sull'indirizzo IP pubblico della scheda di interfaccia di rete associata alla scheda di interfaccia di rete di IIS01; questo indirizzo IP pubblico è disponibile tramite il portale o PowerShell.
2. L'indirizzo IP pubblico passa il traffico alla rete virtuale verso IIS01 (il server Web)
3. La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1. Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2. Regola gruppo di sicurezza di rete 2 (RDP) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
4. Senza regole in uscita, sono applicabili le regole predefinite e il traffico restituito è consentito.
5. La sessione RDP è abilitata.
6. IIS01 richiede il nome utente e la password

>[!NOTE]
>Per eseguire la connessione tramite RDP ai server back-end in questa istanza, il server IIS viene usato come "jumpbox". Eseguire prima la connessione tramite RDP al server IIS e, quindi, dal server IIS eseguire la connessione tramite RDP al server back-end.
>
>

#### <a name="allowed-web-server-dns-look-up-on-dns-server"></a>(*Consentito*) Ricerca DNS del server Web sul server DNS
1. Il server Web, IIS01, richiede un feed di dati all'indirizzo www.data.gov, ma deve risolvere l'indirizzo.
2. La configurazione di rete per la rete virtuale elenca DNS01 (10.0.2.4 nella subnet back-end) come server DNS primario, IIS01 invia la richiesta DNS a DNS01.
3. Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
4. La subnet back-end inizia l'elaborazione delle regole in ingresso:
  * Regola gruppo di sicurezza di rete 1 (DNS) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
5. Il server DNS riceve la richiesta.
6. Il server DNS non ha l'indirizzo memorizzato nella cache e invia la richiesta a un server DNS radice su Internet.
7. Non sono impostate regole in uscita sulla subnet back-end, il traffico è consentito.
8. Il server DNS Internet risponde perché la sessione è stata avviata internamente, la risposta è consentita.
9. Il server DNS memorizza la risposta nella cache e restituisce a IIS01 la risposta alla richiesta iniziale.
10. Non sono impostate regole in uscita sulla subnet back-end, il traffico è consentito.
11. La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1. Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2. La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
12. IIS01 riceve la risposta da DNS01.

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Consentito*) Il server Web richiede l'accesso a un file in AppVM01
1. IIS01 richiede un file in AppVM01.
2. Non sono impostate regole in uscita sulla subnet front-end, il traffico è consentito.
3. La subnet back-end inizia l'elaborazione delle regole in ingresso:
  1. Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2. Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3. Regola gruppo di sicurezza di rete 3 (da Internet a IIS01) non applicabile, passa alla regola successiva.
  4. Regola gruppo di sicurezza di rete 4 (da IIS01 ad AppVM01) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
4. AppVM01 riceve la richiesta e risponde con il file (presupponendo che l'accesso sia autorizzato).
5. Non essendoci regole in uscita sulla subnet back-end, la risposta è consentita
6. La subnet front-end inizia l'elaborazione delle regole in ingresso:
  1. Non sono presenti regole del gruppo di sicurezza di rete applicabili al traffico in ingresso dalla subnet back-end alla subnet front-end, quindi nessuna regola del gruppo di sicurezza di rete è applicabile.
  2. La regola di sistema predefinita che consente il traffico tra le subnet consentirebbe questo tipo di traffico, perciò è consentito.
7. Il server IIS riceve il file.

#### <a name="denied-rdp-to-backend"></a>(*Negato*) Traffico RDP al back-end
1. Un utente su Internet tenta di eseguire la connessione tramite RDP al server AppVM01.
2. Non essendoci indirizzi IP pubblici associati alla scheda di interfaccia di rete dei server, il traffico non entra nella rete virtuale e non raggiunge il server.
3. Se tuttavia per qualunque motivo è stato abilitato un indirizzo IP pubblico, la regola del gruppo di sicurezza di rete 2 (RDP) consentirà questo traffico.

>[!NOTE]
>Per eseguire la connessione tramite RDP ai server back-end in questa istanza, il server IIS viene usato come "jumpbox". Eseguire prima la connessione tramite RDP al server IIS e, quindi, dal server IIS eseguire la connessione tramite RDP al server back-end.
>
>

#### <a name="denied-web-to-backend-server"></a>(*Negato*) Traffico Web al server back-end
1. Un utente su Internet tenta di accedere a un file su AppVM01
2. Non essendoci indirizzi IP pubblici associati alla scheda di interfaccia di rete dei server, il traffico non entra nella rete virtuale e non raggiunge il server.
3. Se per qualunque motivo è stato abilitato un indirizzo IP pubblico, la regola del gruppo di sicurezza di rete 5 (da Internet a rete virtuale) bloccherà questo traffico.

#### <a name="denied-web-dns-look-up-on-dns-server"></a>(*Negato*) Ricerca DNS Web sul server DNS
1. Un utente su Internet tenta di cercare un record DNS interno su DNS01
2. Non essendoci indirizzi IP pubblici associati alla scheda di interfaccia di rete dei server, il traffico non entra nella rete virtuale e non raggiunge il server.
3. Se per qualunque motivo è stato abilitato un indirizzo IP pubblico, la regola del gruppo di sicurezza di rete 5 (da Internet a rete virtuale) bloccherà il traffico. Nota: la regola 1 (DNS) potrebbe non essere applicabile perché l'indirizzo di origine della richieste è Internet e la regola 1 si applica solo alla rete locale virtuale come origine.

#### <a name="denied-sql-access-on-the-web-server"></a>(*Negato*) Accesso SQL nel server Web
1. Un utente su Internet richiede dati SQL da IIS01
2. Non essendoci indirizzi IP pubblici associati alla scheda di interfaccia di rete dei server, il traffico non entra nella rete virtuale e non raggiunge il server.
3. Se per qualunque motivo è stato abilitato un indirizzo IP pubblico, la subnet front-end inizia l'elaborazione delle regole in ingresso:
  1. Regola gruppo di sicurezza di rete 1 (DNS) non applicabile, passa alla regola successiva.
  2. Regola gruppo di sicurezza di rete 2 (RDP) non applicabile, passa alla regola successiva.
  3. Regola gruppo di sicurezza di rete 3 (da Internet a IIS01) applicabile, il traffico è consentito, l'elaborazione delle regole si arresta.
4. Il traffico raggiunge l'indirizzo IP interno di IIS01 (10.0.1.5).
5. IIS01 non è in ascolto sulla porta 1433, pertanto la richiesta non ottiene risposta.

## <a name="conclusion"></a>Conclusioni
Questo esempio consente di isolare la subnet back-end dal traffico in ingresso in un modo relativamente semplice e diretto.

Altri esempi e una panoramica dei limiti di sicurezza della rete sono disponibili [qui][HOME].

## <a name="references"></a>Riferimenti
### <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
Questo esempio usa un modello di Azure Resource Manager predefinito in un repository GitHub gestito da Microsoft e lo rende disponibile alla community. Il modello può essere distribuito immediatamente da GitHub o scaricato e modificato in base alle specifiche esigenze. 

Il modello principale è presente nel file denominato "azuredeploy.json". Questo modello può essere inviato tramite PowerShell o l'interfaccia della riga di comando (con il file "azuredeploy.parameters.json associato") per la distribuzione. Il modo più semplice è usare il pulsante "Deploy to Azure" nella pagina README.md in GitHub.

Per distribuire il modello che usa questo esempio da GitHub e il portale di Azure, seguire questi passaggi:

1. Da un browser passare a [Template][Template]
2. Fare clic sul pulsante "Deploy to Azure" oppure su "View" per visualizzare una rappresentazione grafica del modello.
3. Immettere l'account di archiviazione, il nome utente e la password nel pannello dei parametri, quindi fare clic su **OK**
5. Creare un gruppo di risorse per questa distribuzione. È possibile usarne uno esistente, ma è consigliabile creare una nuova istanza per risultati ottimali.
6. Se necessario, modificare le impostazioni relative a Sottoscrizione e Località per la rete virtuale.
7. Fare clic su **Rivedere le note legali**, leggere le condizioni e fare clic su **Acquista** per accettare.
8. Fare clic su **Crea** per iniziare la distribuzione di questo modello.
9. Dopo il corretto completamento della distribuzione, passare al gruppo di risorse creato per questa distribuzione per esaminare le risorse configurate in esso.

>[!NOTE]
>Questo modello consente di eseguire la connessione tramite RDP solo al server IIS01; l'indirizzo IP pubblico per IIS01 è reperibile nel portale. Per eseguire la connessione tramite RDP ai server back-end in questa istanza, il server IIS viene usato come "jumpbox". Eseguire prima la connessione tramite RDP al server IIS e, quindi, dal server IIS eseguire la connessione tramite RDP al server back-end.
>
>

Per rimuovere questa distribuzione, eliminare il gruppo di risorse; verranno eliminate anche tutte le risorse figlio.

#### <a name="sample-application-scripts"></a>Script di applicazione di esempio
Dopo la corretta esecuzione del modello è possibile configurare il server Web e il server applicazioni con un'applicazione Web di esempio per consentire l'esecuzione di test con questa configurazione della rete perimetrale. Per installare un'applicazione di esempio per questo e altri esempi di rete perimetrale, è possibile trovarne una in [Script di applicazione di esempio][SampleApp]

## <a name="next-steps"></a>Passaggi successivi

* Distribuire questo esempio
* Compilare l'applicazione di esempio
* Testare diversi flussi di traffico attraverso la rete perimetrale

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-arm/example1design.png "Rete perimetrale in ingresso con gruppo di sicurezza di rete"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[Template]: https://github.com/Azure/azure-quickstart-templates/tree/master/301-dmz-nsg
[SampleApp]: ./virtual-networks-sample-app.md