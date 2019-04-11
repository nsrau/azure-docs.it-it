---
title: 'Esempio di rete perimetrale: compilazione una rete perimetrale per proteggere le applicazioni con un firewall e Nsg | Microsoft Docs'
description: Creare una rete perimetrale con firewall e gruppi di sicurezza di rete (Nsg)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: c78491c7-54ac-4469-851c-b35bfed0f528
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: e0271c9212b093bd803518ebeaa4b7d9682cc773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997460"
---
# <a name="example-2-build-a-perimeter-network-to-protect-applications-with-a-firewall-and-nsgs"></a>Esempio 2 Creare una rete perimetrale per proteggere le applicazioni con un firewall e Nsg
[Tornare alla pagina di sicurezza di rete e servizi cloud Microsoft][HOME]

In questo esempio viene illustrato come creare una rete perimetrale (detta anche *rete Perimetrale* e *sottorete*) con un firewall, quattro i computer Windows Server, e gruppi di sicurezza di rete (Nsg). Include informazioni dettagliate su ogni comando rilevante per fornire una conoscenza più approfondita di ogni passaggio. La sezione "Scenari di traffico" fornisce una spiegazione dettagliata del modo in cui il traffico procede attraverso i livelli di difesa della rete perimetrale. Infine, la sezione "Riferimenti" fornisce il codice completo e le istruzioni su come creare l'ambiente per testare e sperimentare vari scenari.

![Rete perimetrale con Appliance virtuale di rete e gli Nsg in ingresso][1]

## <a name="environment"></a>Environment 
Questo esempio è basato su uno scenario con una sottoscrizione di Azure che contiene questi elementi:

* Due servizi cloud: FrontEnd001 e BackEnd001.
* Una rete virtuale denominata CorpNetwork con due subnet: Front-end e back-end.
* Un singolo NSG che viene applicato a entrambe le subnet.
* Appliance virtuale di rete: Barracuda NextGen Firewall connesso alla subnet front-end.
* Un computer Windows Server che rappresenta un server web applicazioni: IIS01.
* Due computer Server Windows che rappresentano server back-end dell'applicazione: AppVM01 e AppVM02.
* Un computer Windows Server che rappresenta un server DNS: DNS01.

> [!NOTE]
> Sebbene questo esempio Usa Barracuda NextGen Firewall, è possibile utilizzare molte Appliance virtuali di rete.
> 
> 

Lo script di PowerShell nella sezione "Riferimenti" di questo articolo si basa la maggior parte dell'ambiente descritto di seguito. Le macchine virtuali e reti virtuali vengono compilate mediante lo script, ma tali processi non sono descritti in dettaglio in questo documento.

Per creare l'ambiente, eseguire queste operazioni:

1. Salvare il file XML di configurazione di rete incluso nella sezione "Riferimenti" (aggiornato con i nomi, indirizzi e l'IP indirizzi in modo che corrisponda allo scenario).
2. Aggiornare le variabili definite dall'utente nello script di PowerShell in modo che corrispondano all'ambiente che di script verrà eseguito contro (sottoscrizioni, nomi di servizio e così via).
3. Eseguire lo script di PowerShell.

> [!NOTE]
> L'area specificata nello script di PowerShell deve corrispondere all'area specificata nel file XML di configurazione di rete.
>
>

Dopo la corretta esecuzione dello script, è possibile completare questi passaggi:

1. Configurare le regole del firewall. Vedere la sezione "Regole del Firewall" di questo articolo.
2. Se si desidera, è possibile configurare il server web e il server applicazioni con una semplice applicazione web per consentire la verifica con la configurazione della rete perimetrale. È possibile usare gli script di two applicazione forniti nella sezione "Riferimenti".

La sezione successiva spiega la maggior parte delle istruzioni dello script correlati al Nsg.

## <a name="nsgs"></a>Gruppi di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono poi caricate sei regole.

> [!TIP]
> In generale, è necessario creare prima di tutto le regole specifiche di "Consenti" e l'ultimo di regole "Nega" più generiche. I controlli di priorità assegnati quali regole vengono valutate per prime. Una volta individuato il traffico che si applica a una regola specifica, non altre regole vengono valutate. Possono applicare le regole NSG in ingresso o la direzione in uscita (dal punto di vista della subnet).
> 
> 

In modo dichiarativo, queste regole vengono create per il traffico in ingresso:

1. Il traffico DNS interno (porta 53) è consentito.
2. È consentito il traffico RDP (porta 3389) da internet a qualsiasi macchina virtuale.
3. È consentito il traffico HTTP (porta 80) da internet per l'Appliance virtuale di rete (firewall).
4. Tutto il traffico (tutte le porte) da IIS01 ad AppVM01 è consentito.
5. Tutto il traffico (tutte le porte) da internet all'intera rete virtuale (entrambe le subnet) viene negato.
6. Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene negato.

Con queste regole associate a ogni subnet, se una richiesta HTTP in ingresso da internet al server web, entrambi regola 3 (Consenti) e la regola 5 (Nega) potrebbe sembrare che si applicano, ma poiché la regola 3 ha una priorità più alta, verrà applicato solo. Regola 5 non verrà presa in considerazione. Pertanto, la richiesta HTTP potrà essere il firewall.

Se lo stesso traffico prova a raggiungere il server DNS01, la regola 5 (Nega) sarà il primo da applicare, in modo che il traffico non autorizzato a passare al server. Regola 6 (Nega) Blocca la subnet front-end di comunicare con la subnet back-end (ad eccezione del traffico consentito nelle regole 1 e 4). Ciò consente di proteggere la rete back-end nel caso in cui un utente malintenzionato comprometta l'applicazione web sul front-end. In tal caso, l'utente malintenzionato sarebbe abbia accesso limitato alla rete back-end "protetta". (L'autore dell'attacco potrebbe essere in grado di accedere solo alle risorse esposte nel server AppVM01).

È presente una regola in uscita predefinita che consente il traffico in uscita a internet. In questo esempio, si sta che consenta il traffico in uscita e non modifica le regole in uscita. Per bloccare il traffico in entrambe le direzioni, è necessario il routing definito dall'utente. Altre informazioni su questa tecnica in un altro esempio nel [documento limiti di sicurezza principali][HOME].

Le regole NSG descritte di seguito sono simili alle regole NSG nella [esempio 1: creare una semplice rete Perimetrale con Nsg][Example1]. Esaminare la descrizione di NSG in tale articolo per un approfondimento su ogni regola di sicurezza di rete e i relativi attributi.

## <a name="firewall-rules"></a>Regole del firewall
È necessario installare un client di gestione in un computer per gestire il firewall e creare le configurazioni necessarie. Vedere la documentazione fornita dal firewall (o altre Appliance virtuale di rete) del fornitore sulla gestione del dispositivo. La parte restante di questa sezione descrive la configurazione del firewall stesso tramite il client di gestione del fornitore (non il portale di Azure o PowerShell).

Visualizzare [Barracuda NG Admin](https://techlib.barracuda.com/NG61/NGAdmin) per le istruzioni per scaricare il client e la connessione al firewall Barracuda usato in questo esempio.

È necessario creare le regole di inoltro sul firewall. Poiché lo scenario illustrato in questo esempio instrada solo il traffico internet in ingresso verso il firewall e quindi al server web, è necessario solo l'inoltro di una regola NAT. Sul firewall Barracuda usato in questo esempio, la regola sarà una regola Destination NAT (Dst NAT) per passare il traffico.

Per creare la regola seguente (o per verificare le regole predefinite esistenti), completare questi passaggi:
1. Nel dashboard del client Barracuda NG Admin, nella scheda configurazione nella **Operational Configuration** sezione, selezionare **Ruleset**. 

   Una griglia di denominata **Main Rules** Mostra esistenti attive e disattivate le regole del firewall.

2. Per creare una nuova regola, selezionare il piccolo green **+** pulsante nell'angolo superiore destro della griglia. (Il firewall potrebbe essere bloccato. Se viene visualizzato un pulsante contrassegnato **blocco** e non è possibile creare o modificare le regole, selezionare il pulsante per sbloccare il set di regole e consentire la modifica.)
  
3. Per modificare una regola esistente, selezionare la regola, pulsante destro del mouse e quindi selezionare **Modifica regola**.

Creare una nuova regola denominata simile **WebTraffic.** 

L'icona della regola Destination NAT è simile alla seguente:  ![Icona di Destination NAT][2]

La regola stessa avrà un aspetto simile al seguente:

![Regola del firewall][3]

Tutti gli indirizzi in ingresso che raggiunge il firewall al fine di raggiungere HTTP (porta 80 o 443 per HTTPS) verranno inviato all'esterno di interfaccia di DHCP1 Local IP del firewall e reindirizzati al server web con l'indirizzo IP 10.0.1.5. Poiché il traffico in entrata sulla porta 80 e passare al server web sulla porta 80, non è necessaria alcuna modifica della porta. Ma l'elenco di destinazione avrebbe potuto essere 10.0.1.5:8080 se il server web in ascolto sulla porta 8080, che converte la porta in ingresso 80 sul firewall alla porta in ingresso 8080 sul server web.

È anche necessario specificare un metodo di connessione. Per la regola di destinazione da internet, Dynamic SNAT è il metodo più appropriato.

Anche se solo creati una sola regola, è importante impostare correttamente le priorità. Nella griglia di tutte le regole del firewall, se questa nuova regola si trova nella parte inferiore (sotto la regola BLOCKALL), non verrà mai entrano in gioco. Assicurarsi che la nuova regola per il traffico web è sopra la regola BLOCKALL.

Dopo aver creata la regola, è necessario eseguirne il push al firewall e quindi attivarlo. Se non si esegue questi passaggi, la modifica della regola non avrà effetto. Nella sezione successiva descrive il processo di push e attivazione.

## <a name="rule-activation"></a>Attivazione delle regole
Ora che la regola viene aggiunto all'insieme di regole, è necessario caricare il set di regole del firewall e attivarlo.

![Attivazione delle regole firewall][4]

Nell'angolo superiore destro del client di gestione, si noterà un gruppo di pulsanti. Selezionare **Send Changes** per inviare il set di regole modificate al firewall e quindi selezionare **Activate**.

Ora che hai attivato il set di regole firewall, l'ambiente è stata completata. Se si desidera, è possibile eseguire gli script di applicazione di esempio nella sezione "Riferimenti" per aggiungere un'applicazione all'ambiente. Se si aggiunge un'applicazione, è possibile testare gli scenari di traffico descritti nella sezione successiva.

> [!IMPORTANT]
> È necessario tenere presente che non si raggiungerà il server web direttamente. Quando un browser richiede una pagina HTTP da FrontEnd001.CloudApp.Net, l'endpoint HTTP (porta 80) passa il traffico al firewall, non per il server web. Il firewall, quindi, a causa della regola creata in precedenza, Usa il processo NAT per eseguire il mapping della richiesta al server web.
> 
> 

## <a name="traffic-scenarios"></a>Scenari di traffico
#### <a name="allowed-web-to-web-server-through-the-firewall"></a>(Consentito) Web al server web attraverso il firewall
1. Un utente internet richiede una pagina HTTP a frontend001.cloudapp.NET (servizio cloud con connessione internet).
2. Il servizio cloud passa il traffico attraverso un endpoint aperto sulla porta 80 all'interfaccia locale del firewall su 10.0.1.4: 80.
3. La subnet front-end inizia l'elaborazione delle regole in ingresso:
   1. Regola di sicurezza di rete 1 (DNS) non è applicabile. Sposta alla regola successiva.
   2. Regola di sicurezza di rete 2 (RDP) non è applicabile. Sposta alla regola successiva.
   3. Applicare la regola di sicurezza di rete 3 (da internet a firewall). Consentire il traffico. Arrestare l'elaborazione della regola.
4. Il traffico raggiunge l'indirizzo IP interno del firewall (10.0.1.4).
5. Un regola di inoltro di firewall determina che si tratti di traffico di porta 80 e lo reindirizza al server web IIS01.
6. Iis01 è in ascolto per il traffico web, riceve la richiesta e inizia l'elaborazione della richiesta.
7. Iis01 richiede le informazioni dall'istanza di SQL Server in AppVM01.
8. Non sono presenti regole in uscita sulla subnet front-end, in modo che il traffico è consentito.
9. La subnet back-end inizia l'elaborazione delle regole in ingresso:
   1. Regola di sicurezza di rete 1 (DNS) non è applicabile. Sposta alla regola successiva.
   2. Regola di sicurezza di rete 2 (RDP) non è applicabile. Sposta alla regola successiva.
   3. Non è applicabile la regola di sicurezza di rete 3 (da internet a firewall). Sposta alla regola successiva.
   4. Regola di sicurezza di rete 4 (da IIS01 ad AppVM01) è applicabile. Consentire il traffico. Arrestare l'elaborazione della regola.
10. Istanza di SQL Server in AppVM01 riceve la richiesta e risponde.
11. Perché non sono presenti regole in uscita sulla subnet back-end, la risposta è consentita.
12. La subnet front-end inizia l'elaborazione delle regole in ingresso:
    1. Non sono presenti regole di sicurezza di rete che si applica al traffico dalla subnet back-end alla subnet front-end, in ingresso in modo che nessuna delle regole NSG si applicano.
    2. La regola di sistema predefinita che consente il traffico tra subnet consente il traffico, in modo che il traffico è consentito.
13. Iis01 riceve la risposta da AppVM01, completa la risposta HTTP e lo invia al richiedente.
14. Poiché si tratta di una sessione NAT dal firewall, la destinazione della risposta è inizialmente il firewall.
15. Il firewall riceve la risposta dal server del web e la inoltra all'utente internet.
16. Poiché non sono presenti regole in uscita sulla subnet front-end, la risposta è consentita e l'utente internet riceve la pagina web.

#### <a name="allowed-rdp-to-backend"></a>(Consentito) Connessione tramite RDP al back-end
1. Un amministratore del server su internet richiede una sessione RDP ad AppVM01 su BackEnd001.CloudApp.Net:*xxxxx*, dove *xxxxx* è il numero di porta assegnato casualmente per RDP a AppVM01. (È possibile trovare la porta assegnata sul portale di Azure o tramite PowerShell).
2. Poiché il firewall è in ascolto solo dell'indirizzo FrontEnd001.CloudApp.Net, non è interessato con questo flusso di traffico.
3. La subnet back-end inizia l'elaborazione delle regole in ingresso:
   1. Regola di sicurezza di rete 1 (DNS) non è applicabile. Sposta alla regola successiva.
   2. Applicare la regola di sicurezza di rete 2 (RDP). Consentire il traffico. Arrestare l'elaborazione della regola.
4. Poiché non sono presenti regole in uscita, le regole predefinite si applicano e restituiscono il traffico è consentito.
5. La sessione RDP è abilitata.
6. AppVM01 richiede un nome utente e password.

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Consentito) Ricerca DNS nel server Web sul server DNS
1. Le esigenze del server, IIS01, web un dati feed in www.data.gov, ma devono risolvere l'indirizzo.
2. La configurazione di rete per le presentazioni di rete virtuale DNS01 (10.0.2.4 sulla subnet back-end) come server DNS primario. Iis01 invia la richiesta DNS a DNS01.
3. Perché non sono presenti regole in uscita sulla subnet front-end, il traffico è consentito.
4. La subnet back-end inizia l'elaborazione delle regole in ingresso:
   1. Si applica la regola di sicurezza di rete 1 (DNS). Consentire il traffico. Arrestare l'elaborazione della regola.
5. Il server DNS riceve la richiesta.
6. Il server DNS non ha l'indirizzo memorizzato nella cache e un server DNS radice su internet.
7. Perché non sono presenti regole in uscita sulla subnet back-end, il traffico è consentito.
8. Il DNS internet risponde server. Poiché la sessione è stata avviata internamente, la risposta è consentita.
9. Il server DNS memorizza nella cache la risposta e risponde alla richiesta da IIS01.
10. Perché non sono presenti regole in uscita sulla subnet back-end, il traffico è consentito.
11. La subnet front-end inizia l'elaborazione delle regole in ingresso:
    1. Non sono presenti regole di sicurezza di rete che si applica al traffico dalla subnet back-end alla subnet front-end, in ingresso in modo che nessuna delle regole NSG si applicano.
    2. La regola di sistema predefinita che consente il traffico tra subnet consente il traffico, in modo che il traffico è consentito.
12. Iis01 riceve la risposta da DNS01.

#### <a name="allowed-web-server-file-access-on-appvm01"></a>(Consentito) Accesso ai file di Web server in AppVM01
1. Iis01 richiede un file in AppVM01.
2. Perché non sono presenti regole in uscita sulla subnet front-end, il traffico è consentito.
3. La subnet back-end inizia l'elaborazione delle regole in ingresso:
   1. Regola di sicurezza di rete 1 (DNS) non è applicabile. Sposta alla regola successiva.
   2. Regola di sicurezza di rete 2 (RDP) non è applicabile. Sposta alla regola successiva.
   3. Non è applicabile la regola di sicurezza di rete 3 (da internet a firewall). Sposta alla regola successiva.
   4. Regola di sicurezza di rete 4 (da IIS01 ad AppVM01) è applicabile. Consentire il traffico. Arrestare l'elaborazione della regola.
4. AppVM01 riceve la richiesta e risponde con il file (presupponendo che l'accesso sia autorizzato).
5. Perché non sono presenti regole in uscita sulla subnet back-end, la risposta è consentita.
6. La subnet front-end inizia l'elaborazione delle regole in ingresso:
   1. Non sono presenti regole di sicurezza di rete che si applica al traffico dalla subnet back-end alla subnet front-end, in ingresso in modo che nessuna delle regole NSG si applicano.
   2. La regola di sistema predefinita che consente il traffico tra subnet consente il traffico, in modo che il traffico è consentito.
7. Iis01 riceve il file.

#### <a name="denied-web-direct-to-web-server"></a>(Negato) Web diretto al server web
Poiché il firewall e server web IIS01 sono nello stesso servizio cloud, condividono lo stesso indirizzo IP pubblico. Quindi, tutto il traffico HTTP viene indirizzato al firewall. Mentre una richiesta viene servita correttamente, può passare direttamente al server web. Ha esito positivo, come progettato, attraverso il firewall prima di tutto. Vedere il primo scenario in questa sezione per il flusso del traffico.

#### <a name="denied-web-to-backend-server"></a>(Negato) Web server back-end
1. Un utente internet prova ad accedere a un file in AppVM01 tramite il servizio BackEnd001.CloudApp.Net.
2. Poiché non esistono Nessun endpoint aperto per la condivisione di file, questo non passa attraverso il servizio cloud e non raggiunge il server.
3. Se gli endpoint sono aperti per qualche motivo, la regola di sicurezza di rete 5 (da internet a rete virtuale) Blocca il traffico.

#### <a name="denied-web-dns-lookup-on-the-dns-server"></a>(Negato) Ricerca DNS Web sul server DNS
1. Un utente internet prova a cercare un record DNS interno su DNS01 tramite il servizio BackEnd001.CloudApp.Net.
2. Poiché non sono presenti endpoint aperti per DNS, questo non passa attraverso il servizio cloud e non raggiunge il server.
3. Se gli endpoint sono aperti per qualche motivo, la regola di sicurezza di rete 5 (da internet a rete virtuale) Blocca il traffico. (Regola 1 [DNS] non è applicabile per due motivi. In primo luogo, l'indirizzo di origine è internet e questa regola si applica solo quando la rete virtuale locale è l'origine. In secondo luogo, questa regola è una regola di assenso, pertanto non blocca mai il traffico.)

#### <a name="denied-web-to-sql-access-through-the-firewall"></a>(Negato) Web per l'accesso a SQL tramite il firewall
1. Un utente internet richiede dati SQL frontend001.cloudapp.NET (un servizio cloud con connessione internet).
2. Poiché non sono presenti endpoint aperti per SQL, questo non passa attraverso il servizio cloud e non raggiunge il firewall.
3. Se gli endpoint sono aperti per qualche motivo, la subnet front-end inizia l'elaborazione delle regole in ingresso:
   1. Regola di sicurezza di rete 1 (DNS) non è applicabile. Sposta alla regola successiva.
   2. Regola di sicurezza di rete 2 (RDP) non è applicabile. Sposta alla regola successiva.
   3. Applicare la regola di sicurezza di rete 3 (da internet a firewall). Consentire il traffico. Arrestare l'elaborazione della regola.
4. Il traffico raggiunge l'indirizzo IP interno del firewall (10.0.1.4).
5. Il firewall non ha nessuna regola di inoltro per SQL ed elimina il traffico.

## <a name="conclusion"></a>Conclusioni
Questo esempio illustra un modo relativamente semplice per proteggere l'applicazione con un firewall e isolare la subnet back-end dal traffico in ingresso.

È possibile trovare altri esempi e una panoramica della rete i limiti di sicurezza [Ecco][HOME].

## <a name="references"></a>Riferimenti
### <a name="full-script-and-network-config"></a>Completo dello script e configurazione di rete
Salvare lo script seguente in un file di script di PowerShell. Salvare lo script di configurazione di rete in un file denominato NetworkConf2.xml.
Modificare le variabili definite dall'utente in base alle esigenze. Eseguire lo script e quindi seguire le istruzioni nella sezione "Regole del Firewall" di questo articolo.

#### <a name="full-script"></a>Script completo
Questo script, sulla base delle variabili definite dall'utente, verrà completata la procedura seguente:

1. Connettersi a una sottoscrizione di Azure.
2. Creare un account di archiviazione.
3. Creare una rete virtuale e due subnet, come definito nel file di configurazione di rete.
4. Compilare quattro macchine virtuali Windows Server.
5. Configurare sicurezza di rete. Configurazione completa questi passaggi:
   * Crea un gruppo di sicurezza.
   * Popola il gruppo di sicurezza di rete con regole.
   * Associa il gruppo di sicurezza di rete alle subnet appropriate.

Eseguire questo script di PowerShell in locale in un server o computer connesso a internet.

> [!IMPORTANT]
> Quando si esegue questo script, avvisi e altri messaggi informativi potrebbero sembrare in PowerShell. È sufficiente essere preoccupati per i messaggi di errore vengono visualizzati in rosso.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of a perimeter network and Network Security Groups in an isolated network. (Azure only. No hybrid connections.)

     .DESCRIPTION
      This script will build out a sample perimeter network setup containing:
       - A default storage account for VM disks.
       - Two new cloud services.
       - Two subnets (the FrontEnd and BackEnd subnets).
       - A network virtual appliance (NVA): a Barracuda NextGen Firewall.
       - One server on the FrontEnd subnet (plus the NVA on the FrontEnd subnet).
       - Three servers on the BackEnd subnet.
       - Network Security Groups to allow/deny traffic patterns as declared.

      Before running the script, ensure the network configuration file is created in
      the directory referenced by the $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Security requirements are different for each use case and can be addressed in many ways. Be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that you can use, but it should not be used for all scenarios. You
      are responsible for assessing your security needs and the appropriate protections
      and then effectively implementing those protections.

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User-Defined Global Variables
      # These should be changed to reflect your subscription and services.
      # Invalid options will fail in the validation section.

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # NSG Details
        $NSGName = "MyVNetSG"

    # User-Defined VM-Specific Config
        # To ensure proper NSG rule creation later in this script:
        #       - The web server must be VM 1.
        #       - The AppVM1 server must be VM 2.
        #       - The DNS server must be VM 4.
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG rule IP addresses
        #       are aligned to the associated VM IP addresses.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No user-defined variables or   #
    # configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create storage account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update subscription pointer to new storage account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create virtual network
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the endpoints we'll need once we're up and running.
                # Note: Web traffic goes through the firewall, so we'll need to set up an HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: An SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *

        # Assign the NSG to the subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-Script Manual Configuration
      # Configure firewall
      # Install test web app (run post-build script on the IIS server)
      # Install back-end resources (run post-build script on AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>File di configurazione di rete
Salvare questo file XML con percorsi aggiornati e quindi aggiungere un collegamento a questo file nella variabile $NetworkConfigFile nello script precedente.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Script di applicazione di esempio
Se si vuole installare un'applicazione di esempio per questo e altri esempi di rete perimetrale, vedere la [script di applicazione di esempio][SampleApp].

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Rete perimetrale in ingresso con gruppo di sicurezza di rete"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icona di Destination NAT"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regola del firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Attivazione delle regole firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
