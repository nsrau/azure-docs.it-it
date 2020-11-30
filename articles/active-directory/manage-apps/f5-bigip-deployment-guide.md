---
title: Guida alla distribuzione di Azure AD Secure ibrido con F5 | Microsoft Docs
description: Esercitazione per la distribuzione della VM F5 BIG-IP Virtual Edition (VE) in Azure IaaS per l'accesso ibrido sicuro
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7555a0b9d7b3336b1020e8f1d9c3445e09afc6f0
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96318223"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Esercitazione per la distribuzione della VM F5 BIG-IP Virtual Edition in Azure IaaS per l'accesso ibrido sicuro

Questa esercitazione illustra il processo end-to-end per la distribuzione di BIG-IP vitural Edition (VE) in Azure IaaS. Al termine di questa esercitazione, è necessario disporre di:

- Una macchina virtuale BIG-IP (VM) completamente preparata per la modellazione di un modello di prova SHA (Secure Hybrid Access)

- Istanza di staging da usare per testare nuovi hotfix e aggiornamenti del sistema BIG-IP

## <a name="prerequisites"></a>Prerequisiti

Prima di F5 l'esperienza BIG-IP o la conoscenza non è necessaria, tuttavia, è consigliabile acquisire familiarità con la [terminologia di F5 Big-IP](https://www.f5.com/services/resources/glossary). La distribuzione di un IP di grandi dimensioni in Azure per SHA richiede:

- Una sottoscrizione di Azure a pagamento o una sottoscrizione di [valutazione](https://azure.microsoft.com/free/)gratuita di 12 mesi.

- Uno qualsiasi dei seguenti SKU delle licenze F5 BIG-IP

  - F5 BIG-IP® Bundle migliore

  - Licenza autonoma di F5 BIG-IP Access Policy Manager™ (APM)

  - F5 la licenza del componente aggiuntivo™ (APM) di gestione dei criteri di accesso BIG IP in un Big-IP F5 BIG-IP® locale di gestione traffico™ (LTM)
  
  - [licenza di valutazione](https://www.f5.com/trial/big-ip-trial.php)completa della funzionalità IP di 90 giorni.

- Un carattere jolly o un certificato di nome alternativo del soggetto (SAN) per pubblicare applicazioni Web tramite Secure Socket Layer (SSL). [La crittografia](https://letsencrypt.org/) consente di ottenere un certificato gratuito di 90 giorni per i test.

- Un certificato SSL per proteggere l'interfaccia di gestione BIG-IPs. È possibile usare un certificato usato per pubblicare le app Web, se il suo soggetto corrisponde al nome di dominio completo (FQDN) del BIG-IP. Ad esempio, un certificato con caratteri jolly definito con Subject *. contoso.com sarà adatto per https://big-ip-vm.contoso.com:8443

La distribuzione delle macchine virtuali e le configurazioni del sistema di base sono necessari circa 30 minuti. a questo punto la piattaforma BIG-IP sarà pronta per l'implementazione di uno degli scenari SHA elencati [qui](f5-aad-integration.md).

Per testare gli scenari, in questa esercitazione si presuppone che BIG-IP venga distribuito in un gruppo di risorse di Azure contenente un ambiente Active Directory (AD). L'ambiente deve essere costituito da un controller di dominio (DC) e da VM host Web (IIS). Anche la presenza di questi server in altre posizioni per la VM BIG-IP è accettabile, in modo da garantire che il BIG-IP includa una linea di base per ognuno dei ruoli necessari per supportare un determinato scenario. Sono supportati anche gli scenari in cui la macchina virtuale BIG-IP è connessa a un altro ambiente tramite una connessione VPN.

Se non si hanno gli elementi indicati qui per il test, è possibile distribuire un intero ambiente di dominio Active Directory in Azure, usando questo [script](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Una raccolta di applicazioni di test di esempio può anche essere distribuita a livello di codice in un host Web IIS mediante questa [automazione con script](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>Il [portale di Azure](https://portal.azure.com/#home) è in continua evoluzione, quindi alcuni passaggi di questa esercitazione potrebbero differire dal layout effettivo osservato nell'portale di Azure.

## <a name="azure-deployment"></a>Distribuzione di Azure

Un BIG-IP può essere distribuito in topologie diverse. Questa guida è incentrata su una singola distribuzione di interfaccia di rete (NIC). Tuttavia, se la distribuzione di BIG-IP richiede più interfacce di rete per la disponibilità elevata, la separazione di rete o più di 1 GB di velocità effettiva, è consigliabile usare i modelli BigIP precompilati [Azure Resource Manager (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html).

Completare le attività seguenti per distribuire BIG-IP VE da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Accedere al [portale di Azure](https://portal.azure.com/#home) con un account che dispone delle autorizzazioni per la creazione di macchine virtuali. Ad esempio, collaboratore

2. Nella casella di ricerca della barra multifunzione superiore digitare **Marketplace**, quindi **immettere**

3. Digitare **F5** nel filtro del Marketplace, quindi **immettere**

4. Selezionare **+ Aggiungi** dalla barra multifunzione superiore e digitare **F5** nel filtro del Marketplace, quindi **immettere**

5. Selezionare **F5 Big-IP Virtual Edition (BYOL)**  >  **selezionare un piano software**  >  **F5 Big-IP ve-all (BYOL, 2 posizioni di avvio)**

6. Selezionare **Crea**.

![L'immagine mostra i passaggi per selezionare un piano software](./media/f5ve-deployment-plan/software-plan.png)

7. Scorrere il menu **nozioni di base** e usare le impostazioni seguenti

 |  Dettagli del progetto     |  Valore     |
 |:-------|:--------|
 |Subscription|Sottoscrizione di destinazione per la distribuzione di macchine virtuali BIG-IP|
 |Resource group | Gruppo di risorse di Azure esistente in cui verrà distribuita la VM BIG-IP o ne verrà creata una. Deve essere lo stesso gruppo di risorse del controller di dominio e delle macchine virtuali IIS|
 | **Dettagli istanza**|  |
 |Nome macchina virtuale| Esempio di BIG-IP-VM |
 |Region | Area geografica di Azure di destinazione per BIG-IP-VM |
 |Opzioni di disponibilità| Abilita solo se si usa la macchina virtuale nell'ambiente di produzione|
 |Immagine| F5 BIG-IP VE-ALL (BYOL, 2 posizioni di avvio)|
 |Istanza Spot di Azure| No, ma è possibile abilitarla se necessario |
 |Dimensione| La specifica minima dovrebbe essere 2 vCPU e 8 GB di memoria|
 |**Account amministratore**|  |
 |Tipo di autenticazione|Selezionare la password per il momento. È possibile passare a una coppia di chiavi in un secondo momento |
 |Nome utente|Identità che verrà creata come account locale BIG-IP per l'accesso alle interfacce di gestione. Il nome utente distingue tra MAIUSCOLe e minuscole.|
 |Password|Proteggere l'accesso amministratore con una password complessa|
 |**Regole porta in ingresso**|  |
 |Porte in ingresso pubbliche|Nessuno|

8. Selezionare **Avanti: i dischi** lasciano tutte le impostazioni predefinite e selezionare **Avanti: rete**.

9. Nel menu **rete** completare queste impostazioni.

 |Interfaccia di rete|      valore |
 |:--------------|:----------------|
 |Rete virtuale|Lo stesso VNet di Azure usato dalle macchine virtuali del controller di dominio e IIS oppure crearne uno|
 |Subnet| Stessa subnet interna di Azure delle VM di controller di dominio e IIS oppure crearne una|
 |IP pubblico |  Nessuno|
 |Gruppo di sicurezza di rete NIC| Selezionare nessuno se la subnet di Azure selezionata nei passaggi precedenti è già associata a un gruppo di sicurezza di rete (NSG); in caso contrario selezionare Basic|
 |Accelera la rete| Off |
 |**Bilanciamento del carico**|     |
 |Bilanciamento del carico della macchina virtuale| No|

10. Selezionare **Avanti: gestione** e completare queste impostazioni.

 |Monitoraggio|    Valore |
 |:---------|:-----|
 |Monitoraggio dettagliato| Off|
 |Diagnostica di avvio|Abilitare con l'account di archiviazione personalizzato. Consente la connessione all'interfaccia di Secure Shell BIG-IP (SSH) tramite l'opzione console seriale nell'portale di Azure. Selezionare un account di archiviazione di Azure disponibile|
 |**Identità**|  |
 |Identità gestita assegnata dal sistema|Off|
 |Azure Active Directory|BIG-IP non supporta attualmente questa opzione|
 |**Arresto automatico**|    |
 |Abilita arresto automatico| Se si sta eseguendo il test, è consigliabile impostare l'arresto giornaliero di BIG-IP-VM|

11. Selezionare **Avanti: avanzate** lasciando tutte le impostazioni predefinite e selezionare **Avanti: Tag**.

12. Selezionare **Avanti: rivedere + crea** per esaminare le configurazioni di Big-IP-VM, prima di selezionare **Crea** per avviare la distribuzione.

13. Il tempo necessario per la distribuzione completa di una VM BIG-IP è in genere di 5 minuti. Al termine, non selezionare **Vai alla risorsa**. espandere invece il menu a sinistra del portale di Azure e selezionare **gruppi di risorse** per passare alla nuova VM Big-IP. Se la creazione della macchina virtuale ha esito negativo, selezionare **indietro** e **Avanti**.

## <a name="network-configuration"></a>Configurazione di rete

Al primo avvio della VM BIG-IP, viene eseguito il provisioning della relativa scheda di interfaccia di rete con un indirizzo IP privato **primario** emesso dal servizio Dynamic Host Configuration Protocol (DHCP) della subnet di Azure a cui è connesso. Questo IP verrà usato dal sistema operativo di gestione traffico di BIG-IP (le TMO) per comunicare con:

- Comunicazione con altri host e servizi

- Accesso in uscita alla rete Internet pubblica

- Accesso in ingresso alle interfacce di gestione di BIG-IPs e configurazione Web e SSH

Esponendo una di queste interfacce di gestione a Internet, si aumenta la superficie di attacco BIG-IPs, quindi perché non è stato effettuato il provisioning dell'IP primario BIG-IPs con un indirizzo IP pubblico durante la distribuzione. Al contrario, verrà eseguito il provisioning di un indirizzo IP interno secondario e di un indirizzo IP pubblico associato per i servizi di pubblicazione.
Questo mapping da 1 a 1 tra un IP pubblico e un indirizzo IP privato della macchina virtuale consente il traffico esterno per raggiungere una macchina virtuale. Tuttavia, una regola NSG di Azure è necessaria anche per consentire il traffico, in modo molto simile a un firewall.

Il diagramma mostra una singola distribuzione NIC di un IP di grandi dimensioni in Azure, configurata con un indirizzo IP primario per operazioni e gestione generali e per gli indirizzi IP di server virtuali distinti per la pubblicazione dei servizi.
In questo modo, una regola NSG consente il traffico remoto destinato a `intranet.contoso.com` essere indirizzato all'indirizzo IP pubblico per il servizio pubblicato, prima di essere inoltrato al server virtuale Big-IP.

![L'immagine mostra la distribuzione ](./media/f5ve-deployment-plan/single-nic-deployment.png) di una singola scheda di interfaccia di rete per impostazione predefinita, gli indirizzi IP pubblici e privati rilasciati alle macchine virtuali di Azure sono sempre dinamici, quindi probabilmente cambiano a ogni riavvio di una macchina virtuale. Evitare problemi di connettività imprevisti modificando il BIG-IPs IP di gestione in statico ed eseguendo la stessa operazione negli indirizzi IP secondari usati per la pubblicazione dei servizi.

1. Dal menu della macchina virtuale Big-IP passare a **Impostazioni**  >  **rete**

2. Nella visualizzazione rete selezionare il collegamento a destra dell' **interfaccia di rete**

![L'immagine mostra la configurazione di rete](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>I nomi delle macchine virtuali vengono generati in modo casuale durante la distribuzione.

3. Nel riquadro a sinistra selezionare **configurazioni IP** e quindi selezionare la riga **Ipconfig1**

4. Impostare l'opzione di **assegnazione IP** su static e, se necessario, modificare l'indirizzo IP primario delle VM Big-IP. Quindi selezionare **Salva** e Chiudi il menu Ipconfig1

>[!NOTE]
>Questo IP primario verrà usato per la connessione e la gestione della BIG-IP-VM.

5. Selezionare **+ Aggiungi** sulla barra multifunzione superiore e specificare un nome per un indirizzo IP privato secondario, ad esempio ipconfig2

6. Impostare l'opzione di **allocazione** delle impostazioni dell'indirizzo IP privato su **statico**. Fornire l'indirizzo IP consecutivo successivo o più basso consente di gestire gli elementi in modo ordinato.

7. Impostare l'indirizzo IP pubblico da **associare** e selezionare **Crea**

8. Specificare un nome per il nuovo indirizzo IP pubblico, ad esempio BIG-IP-VM_ipconfig2_Public

9. Se richiesto, impostare lo **SKU** su standard

10. Se richiesto, impostare il **livello** su **globale** e

11. Impostare l'opzione di **assegnazione** su **static**, quindi selezionare **OK** due volte

Il BIG-IP-VM è ora pronto per la configurazione con:

- **IP privato primario**: dedicato alla gestione della Big-IP-VM tramite l'utilità di configurazione Web e SSH. Verrà usato dal sistema BIG-IP come **indirizzo IP automatico** per connettersi ai servizi back-end pubblicati. Inoltre, si connette a servizi esterni, ad esempio NTP, AD e LDAP.

- **IP privato secondario**: usato quando si crea un server virtuale APM Big-IP per restare in attesa di una richiesta in ingresso a uno o più servizi pubblicati.

- **IP pubblico**: associato all'IP privato secondario, consente il traffico client dalla rete Internet pubblica per raggiungere il server virtuale Big-IP per i servizi pubblicati

Nell'esempio viene illustrata la relazione tra 1 e 1 tra gli indirizzi IP pubblici e privati della macchina virtuale. Una scheda di interfaccia di rete di macchine virtuali di Azure può avere un solo indirizzo IP primario e tutti gli IP creati ulteriormente sono sempre indicati come secondari.

>[!NOTE]
>Sono necessari i mapping IP secondari per la pubblicazione di servizi BIG-IP.

![Questa immagine Mostra tutti gli indirizzi IP secondari](./media/f5ve-deployment-plan/secondary-ips.png)

Per implementare SHA usando la **Configurazione guidata** di Big-IP Access, ripetere i passaggi 5-11 per creare altre coppie private e IP pubbliche per ogni servizio aggiuntivo che si intende pubblicare tramite l'APM Big-IP. Lo stesso approccio può essere usato anche se i servizi di pubblicazione usano BIG-IPs **configurazione avanzata**. In questo scenario, tuttavia, è possibile evitare sovraccarichi IP pubblici usando una configurazione [SNI (Server Name Indicator)](https://support.f5.com/csp/#/article/K13452) . In questa configurazione, un server virtuale BIG-IP accetterà tutto il traffico client ricevuto e lo instraderà alla destinazione.

## <a name="dns-configuration"></a>Configurazione del DNS

È essenziale che DNS sia configurato per consentire ai client di risolvere i servizi SHA pubblicati negli indirizzi IP pubblici della VM BIG-IP.

I passaggi seguenti presuppongono che la zona DNS del dominio pubblico usato per i servizi SHA sia gestita in Azure. Tuttavia, gli stessi principi DNS della creazione del record locator si applicano anche indipendentemente dalla gestione della zona DNS.

1. Se non è già aperto, espandere il menu a sinistra del portale e passare all'opzione BIG-IP-VM tramite i **gruppi di risorse**

2. Dal menu della macchina virtuale Big-IP passare a **Impostazioni**  >  **rete**

3. Nella visualizzazione rete BIG-IP-VM selezionare il primo IP secondario dall'elenco a discesa Configurazione IP e selezionare il collegamento per l' **indirizzo IP pubblico NIC** .

![Screenshot per visualizzare l'IP pubblico della scheda di interfaccia di rete](./media/f5ve-deployment-plan/networking.png)

4. Sotto la sezione **Impostazioni** nel riquadro a sinistra selezionare **configurazione** per visualizzare il menu IP pubblico e proprietà DNS per l'indirizzo IP secondario selezionato

5. Selezionare e **creare** un record alias e selezionare la **zona DNS** dall'elenco a discesa. Se una zona DNS non esiste già, può essere gestita all'esterno di Azure oppure è possibile crearne una per il suffisso di dominio verificato in Azure AD.

6. Usare i dettagli seguenti per creare il primo record di alias DNS:

 | Campo | Valore |
 |:-------|:-----------|
 |Subscription| Stessa sottoscrizione della BIG-IP-VM|
 |Zona DNS| Zona DNS autorevole per il suffisso di dominio verificato che i siti Web pubblicati utilizzeranno, ad esempio, www.contoso.com |
 |Nome | Il nome host specificato verrà risolto nell'indirizzo IP pubblico associato all'IP secondario selezionato. Assicurarsi di definire il DNS corretto per i mapping IP. Vedere la sezione Last Image in networking configs, ad esempio intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Unità TTL | Ore |

7. Selezionare **Crea** per Azure per salvare le impostazioni in DNS pubblico.

8. Lasciare l' **etichetta del nome DNS (facoltativo)** e selezionare **Salva** prima di chiudere il menu IP pubblico.

9. Ripetere i passaggi da 1 a 6 per creare record DNS aggiuntivi per ogni servizio che si intende pubblicare usando la configurazione guidata di BIG-IP.

  Con i record DNS sul posto, è possibile usare uno degli strumenti online come il [controllo DNS](https://dnschecker.org/) per verificare che un record creato sia stato propagato correttamente in tutti i server DNS pubblici globali.

  Se si gestisce lo spazio dei nomi del dominio DNS usando un provider esterno, ad esempio [GoDaddy](https://www.godaddy.com/), sarà necessario creare i record usando la relativa funzione di gestione DNS.

>[!NOTE]
>È anche possibile usare un file hosts locale del PC se si verificano e si scambiano spesso record DNS. Per accedere al file LocalHosts in un PC Windows, è possibile premere Win + R sulla tastiera e inviare i **driver** di Word nella casella Esegui. Tenere presente che un record localhost fornirà solo la risoluzione DNS per il computer locale e non per altri client.

## <a name="client-traffic"></a>Traffico client

Per impostazione predefinita, Azure reti virtuali e le subnet associate sono reti private che non sono in grado di ricevere traffico Internet. La scheda di interfaccia di rete di BIG-IP-VM deve essere collegata al NSG specificato durante la distribuzione. Affinché il traffico Web esterno raggiunga la BIG-IP-VM, è necessario definire una regola di NSG in ingresso per consentire le porte 443 (HTTPS) e 80 (HTTP) attraverso la rete Internet pubblica.

1. Dal menu di **Panoramica** principale della macchina virtuale Big-IP selezionare **rete**

2. Selezionare **Aggiungi** regola connessioni in entrata per immettere le proprietà della regola NSG seguenti:

 |     Campo   |   Valore        |
 |:------------|:------------|
 |Source (Sorgente)| Qualsiasi|
 |Intervalli di porte di origine| *|
 |Indirizzi IP di destinazione|Elenco delimitato da virgole di tutti gli indirizzi IP privati secondari della macchina virtuale di grandi dimensioni|
 |Porte di destinazione| 80,443|
 |Protocollo| TCP |
 |Azione| Consenti|
 |Priorità|Valore minimo disponibile compreso tra 100 e 4096|
 |Nome | Nome descrittivo, ad esempio: `BIG-IP-VM_Web_Services_80_443`|

3. Selezionare **Aggiungi** per confermare le modifiche e chiudere il menu **rete** .

Il traffico HTTP e HTTPS da qualsiasi località potrà ora raggiungere tutte le interfacce secondarie BIG-IP-VM. Consentire la porta 80 è utile per consentire al BIG-IP APM di reindirizzare automaticamente gli utenti da HTTP a HTTPS. Questa regola può essere modificata per aggiungere o rimuovere indirizzi IP di destinazione, se necessario.

## <a name="manage-big-ip"></a>Gestisci BIG-IP

Un sistema BIG-IP viene amministrato tramite l'interfaccia utente di configurazione Web, a cui è possibile accedere utilizzando uno dei metodi consigliati seguenti:

- Da un computer all'interno della rete interna di BIG IP

- Da un client VPN connesso alla rete interna della macchina virtuale BIG-IP-VM

- Pubblicato tramite [Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

È necessario scegliere il metodo più adatto per poter procedere con le configurazioni rimanenti. Se necessario, è possibile connettersi direttamente alla configurazione Web da Internet configurando l'IP primario di BIG-IP con un indirizzo IP pubblico. Aggiungere quindi una regola NSG per consentire il traffico 8443 a tale indirizzo IP primario. Assicurarsi di limitare l'origine a un indirizzo IP attendibile, altrimenti chiunque potrà connettersi.

Al termine, verificare che sia possibile connettersi alla configurazione Web della macchina virtuale BIG-IP e accedere con le credenziali specificate durante la distribuzione della macchina virtuale:

- Se ci si connette da una macchina virtuale nella propria rete interna o tramite VPN, connettersi direttamente alla porta BIG-IPs IP primario e alla porta di configurazione Web. Ad esempio: `https://<BIG-IP-VM_Primary_IP:8443`. Il browser richiederà la connessione non sicura, ma è possibile ignorare la richiesta fino a quando non viene configurato il BIG-IP. Se il browser insiste sul blocco dell'accesso, cancellare la cache e riprovare.

- Se la configurazione Web è stata pubblicata tramite il proxy di applicazione, usare l'URL definito per accedere alla configurazione Web esternamente, senza aggiungere la porta, ad esempio `https://big-ip-vm.contoso.com` . L'URL interno deve essere definito utilizzando la porta di configurazione Web, ad esempio `https://big-ip-vm.contoso.com:8443` 

Un sistema BIG-IP può anche essere gestito tramite l'ambiente SSH sottostante, che in genere viene usato per le attività della riga di comando e l'accesso a livello radice. Sono disponibili diverse opzioni per la connessione all'interfaccia della riga di comando, tra cui:

- [Servizio Bastion di Azure](https://docs.microsoft.com/azure/bastion/bastion-overview): consente connessioni veloci e sicure a qualsiasi macchina virtuale all'interno di una vNET, da qualsiasi posizione

- Connettersi direttamente tramite un client SSH come PuTTy tramite l'approccio JIT

- Console seriale: disponibile nella parte inferiore della sezione supporto e risoluzione dei problemi del menu macchine virtuali nel portale. Non supporta i trasferimenti di file.

- Come per la configurazione Web, è anche possibile connettersi direttamente all'interfaccia della riga di comando da Internet configurando l'IP primario di BIG-IP con un indirizzo IP pubblico e aggiungendo una regola NSG per consentire il traffico SSH. Anche in questo caso, assicurarsi di limitare l'origine a un indirizzo IP attendibile, se si usa questo metodo.  

## <a name="big-ip-license"></a>Licenza BIG-IP

Per poter configurare i servizi di pubblicazione e SHA, è necessario che sia attivato e sottoposta a provisioning di un sistema BIG-IP con il modulo APM.

1. Accedere di nuovo alla configurazione Web e nella pagina delle **proprietà generale** selezionare **Activate (attiva** ).

2. Nel campo **chiave di registrazione di base** immettere la chiave con distinzione tra maiuscole e minuscole fornita da F5.

3. Lasciare il **metodo di attivazione** impostato su **automatico** e selezionare **Avanti**. il Big-IP convaliderà la licenza e visualizzerà il contratto di licenza con l'utente finale (EULA).

4. Selezionare **accetta** e attendere il completamento dell'attivazione, prima di selezionare **continua**.

5. Eseguire nuovamente l'accesso e nella parte inferiore della pagina di riepilogo delle licenze selezionare **Avanti**. Il BIG-IP visualizzerà ora un elenco di moduli che forniscono le varie funzionalità necessarie per SHA. Se questa operazione non viene visualizzata, nella scheda principale passare a **System**  >  **provisioning risorse** di sistema.

6. Controllare la colonna di provisioning per i criteri di accesso (APM)

![L'immagine mostra il provisioning dell'accesso](./media/f5ve-deployment-plan/access-provisioning.png)

7. Selezionare **Invia** e accettare l'avviso

8. Sii paziente e attendi che il BIG-IP completi le nuove funzionalità. Il ciclo può essere eseguito più volte prima di essere completamente inizializzato. 

9. Quando è pronto selezionare **continua** e nella scheda **informazioni** Selezionare **Esegui l'utilità di installazione** .

>[!IMPORTANT]
>Le licenze F5 sono vincolate all'uso da parte di una singola istanza BIG-IP VE alla volta. È possibile che si desideri eseguire la migrazione di una licenza da un'istanza a un'altra e, in tal caso, assicurarsi di [revocare](https://support.f5.com/csp/article/K41458656) la licenza di valutazione nell'istanza attiva prima di rimuoverla. in caso contrario, la licenza andrà persa definitivamente.

## <a name="provision-big-ip"></a>Provisioning di BIG-IP

La protezione del traffico di gestione da e verso BIG-IPs configurazione Web è fondamentale. Configurare un certificato di gestione dei dispositivi per proteggere il canale di configurazione Web dalla compromissione.

1. Dalla barra di spostamento a sinistra passare a **sistema**  >  **Gestione certificati**  >  **traffico** gestione certificati  >  **SSL elenco certificati SSL**  >  **importazione**

2. Dall'elenco a discesa **tipo di importazione** selezionare **PKCS 12 (IIS)** e **scegliere file**. Individuare un certificato Web SSL con nome soggetto o SAN che coprirà l'FQDN a cui verrà assegnata la VM BIG-IP nei passaggi successivi

3. Specificare la password per il certificato e quindi selezionare **Importa** .

4. Dalla barra di spostamento a sinistra passare alla **System**  >  **piattaforma** di sistema

5. Configurare la VM BIG-IP con un nome host completo e il fuso orario per il relativo ambiente, seguito da **Update**

![L'immagine mostra le proprietà generali](./media/f5ve-deployment-plan/general-properties.png)

6. Dalla barra di spostamento a sinistra passare a configurazione di **sistema**  >  **Configuration**  >  **dispositivo**  >  **NTP**

7. Specificare un'origine NTP affidabile e selezionare **Aggiungi**, quindi **Aggiorna**. ad esempio, `time.windows.com`

È ora necessario un record DNS per risolvere il nome FQDN BIG-IPs specificato nei passaggi precedenti, nel relativo indirizzo IP privato primario. È necessario aggiungere un record al DNS interno dell'ambiente o al file localhost di un PC che verrà usato per connettersi alla configurazione Web di BIG-IP. In entrambi i casi, l'avviso del browser non dovrebbe più essere visualizzato quando ci si connette direttamente alla configurazione Web. Ovvero non tramite il proxy di applicazione o qualsiasi altro proxy inverso.

## <a name="ssl-profile"></a>Profilo SSL

Un proxy inverso può fungere da servizio di inoltro semplice, noto anche come proxy trasparente, oppure da un proxy completo che partecipa attivamente agli scambi tra client e server.
Un proxy completo crea due connessioni distinte: una connessione client TCP front-end insieme a una connessione al server TCP back-end separata, abbinata da un gap flessibile al centro. I client si connettono al listener proxy in un'unica estremità, nota anche come **server virtuale**, e il proxy stabilisce una connessione indipendente separata al server back-end. È bidirezionale su entrambi i lati.
In questa modalità proxy completa, il sistema F5 BIG-IP è in grado di ispezionare il traffico e pertanto è possibile interagire anche con le richieste e le risposte. Alcune funzioni, ad esempio il bilanciamento del carico e l'ottimizzazione delle prestazioni Web, oltre a servizi di gestione del traffico più avanzati, come la sicurezza a livello di applicazione, l'accelerazione Web, il routing delle pagine e l'accesso remoto sicuro, si basano su questa funzionalità.
Quando si pubblicano servizi basati su SSL, il processo di decrittografia e crittografia del traffico tra i client e i servizi back-end è gestito da profili di grandi dimensioni IP SSL.

Esistono due tipi di profili:

- **SSL client**: il modo più comune per configurare un sistema Big-IP per la pubblicazione di servizi interni con SSL consiste nel creare un profilo SSL del client. Con un profilo SSL client, un sistema BIG-IP può decrittografare le richieste client in ingresso prima di inviarle a un servizio downstream. Quindi crittografa le risposte back-end in uscita prima di inviarle ai client.

- **Server SSL**: per i servizi back-end configurati per HTTPS, è possibile configurare Big-IP per l'uso di un profilo SSL del server. Con un profilo SSL del server, BIG-IP esegue nuovamente la crittografia della richiesta client prima di inviarla al servizio back-end di destinazione. Quando il server restituisce una risposta crittografata, il sistema BIG-IP decrittografa e crittografa nuovamente la risposta, prima di inviarla al client, tramite il profilo SSL client configurato.

Il provisioning di entrambi i profili SSL client e server avrà il BIG-IP preconfigurato e pronto per tutti gli scenari SHA.

1. Dalla barra di spostamento a sinistra passare a **sistema**  >  **Gestione certificati**  >  **traffico** gestione certificati  >  **SSL elenco certificati SSL**  >  **importazione**

2. Dall'elenco a discesa **tipo di importazione** selezionare **PKCS 12 (IIS)**

3. Specificare un nome per il certificato importato, ad esempio  `ContosoWilcardCert`

4. Selezionare **Scegli file** per selezionare il nome del soggetto del certificato Web SSL corrispondente al suffisso di dominio che si prevede di usare per i servizi pubblicati

5. Specificare la **password** per il certificato importato, quindi selezionare **Importa** .

6. Dalla barra di spostamento a sinistra passare a **local traffic**  >  **profiles**  >  **SSL**  >  **client** e quindi selezionare **Crea**

7. Nella pagina **nuovo profilo SSL del client** specificare un nome descrittivo univoco per il nuovo profilo SSL del client e verificare che il profilo padre sia impostato su **Clientssl**

![L'immagine mostra Update Big-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Selezionare la casella di controllo a destra nella riga della **catena di chiavi del certificato** e selezionare **Aggiungi** .

9. Nei tre elenchi a discesa selezionare il certificato con caratteri jolly importato senza una passphrase, quindi selezionare **Aggiungi**  >  **terminato**.

![L'immagine mostra l'aggiornamento del carattere jolly Big-IP contoso](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Ripetere i passaggi 6-9 per creare un **profilo certificato server SSL**. Dalla barra multifunzione superiore selezionare **SSL**  >  **server** SSL  >  **Crea**.

11. Nella pagina **nuovo profilo SSL server** specificare un nome descrittivo univoco per il nuovo profilo SSL del server e verificare che il profilo padre sia impostato su **serverssl**

12. Selezionare la casella di controllo a destra per il certificato e le righe chiave e nell'elenco a discesa selezionare il certificato importato, quindi **fine**.

![L'immagine mostra l'aggiornamento del profilo tutti i server Big-IP](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Non disperare se non si è in grado di ottenere un certificato SSL, è possibile usare i certificati SSL autofirmati per il server e il client. Nel browser verrà visualizzato solo un errore di certificato.

Un passaggio finale della preparazione di un BIG-IP per SHA consiste nel garantire che sia in grado di individuare le risorse di pubblicazione e anche il servizio directory su cui si basa per SSO. Un BIG-IP ha due origini di risoluzione dei nomi, a partire dal relativo file locale/.../hosts, oppure se non viene trovato un record, il sistema BIG-IP usa qualsiasi servizio DNS con cui è stato configurato. Il metodo del file hosts non si applica ai nodi e ai pool APM che utilizzano un nome di dominio completo.

1. Nel file di configurazione Web passare a configurazione di **sistema**  >  **Configuration**  >  **Device**  >  **DNS** del dispositivo

2. Nell' **elenco server di ricerca DNS** immettere l'indirizzo IP del server DNS degli ambienti

3. Selezionare **Aggiungi**  >  **aggiornamento**

Come passaggio separato e facoltativo, è possibile prendere in considerazione una [configurazione LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) per autenticare gli indirizzi IP di grandi dimensioni in Active Directory anziché gestire gli account IP locali.

## <a name="update-big-ip"></a>Aggiornare BIG-IP

Per sbloccare tutte le nuove funzionalità descritte nelle [linee guida basate su scenari](f5-aad-integration.md), è necessario aggiornare la macchina virtuale Big-IP-VM. È possibile controllare la versione di Systems le TMO posizionando il puntatore del mouse sul nome host BIG-IPs nella parte superiore sinistra della pagina principale. È consigliabile eseguire versione 15. x e versioni successive, ottenibili dal Download di [F5](https://downloads.f5.com/esd/productlines.jsp). Usare le [linee guida](https://support.f5.com/csp/article/K34745165) per aggiornare il sistema operativo principale (le TMO).

Se non è possibile aggiornare la le TMO principale, valutare almeno l'aggiornamento della configurazione guidata da solo usando questi passaggi.

1. Dalla scheda principale della configurazione Web Big-IP passare ad **Access**  >  **Configurazione guidata**

2. Nella pagina **Configurazione guidata** selezionare **Aggiorna configurazione guidata**

![L'immagine mostra come aggiornare Big-IP](./media/f5ve-deployment-plan/update-big-ip.png)

3. Nella finestra di dialogo **aggiornamento guidato configurazione** **scegliere file** per caricare il pacchetto del caso d'uso scaricato e selezionare **carica e installa** .

4. Al termine dell'aggiornamento, selezionare **continua**.

## <a name="backup-big-ip"></a>BIG-IP di backup

Con il sistema BIG-IP ora sottoposta a provisioning completo, è consigliabile eseguire un backup completo della relativa configurazione:

1. Vai a archivi di **sistema**  >  **Archives**  >  **Crea**

2. Specificare un **nome file** univoco e abilitare la **crittografia** con una passphrase

3. Impostare l'opzione **chiavi private** su **Includi** anche per eseguire il backup del dispositivo e dei certificati SSL

4. Selezionare **terminato** e attendere il completamento del processo

5. Verrà visualizzato lo stato di un'operazione specificando lo stato del risultato del backup. Selezionare **OK**.

6. Salvare l'archivio del set di configurazione utente (UCS) localmente scegliendo il collegamento del backup e selezionare **Scarica**.

Come passaggio facoltativo, è anche possibile eseguire un backup dell'intero disco del sistema usando gli [snapshot di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk), che a differenza del backup della configurazione Web fornirebbe alcune contingenze per il testing tra le versioni di le TMO o il rollback a un nuovo sistema.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Ripristinare BIG-IP

Il ripristino di un BIG-IP segue una procedura simile al backup e può essere usato anche per la migrazione di configurazioni tra macchine virtuali di grandi dimensioni. Prima di importare un backup, è consigliabile osservare i percorsi di aggiornamento supportati.

1. Vai a **System**  >  **archivi** di sistema

2. Scegliere il collegamento di un backup da ripristinare oppure selezionare il pulsante **carica** per passare a un archivio UCS salvato in precedenza che non è incluso nell'elenco

3. Specificare la passphrase per il backup e selezionare **Ripristina** .

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Al momento della stesura del documento, il cmdlet AzVmSnapshot è limitato al ripristino dello snapshot più recente, in base alla data. Gli snapshot vengono archiviati nella radice del gruppo di risorse della macchina virtuale. Tenere presente che il ripristino degli snapshot riavvia una macchina virtuale di Azure.

## <a name="additional-resources"></a>Risorse aggiuntive

-   [Reimposta la password di BIG-IP VE in Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Reimpostare la password senza usare il portale](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Modificare la scheda di interfaccia di rete usata per la gestione di BIG IP VE](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Informazioni sulle route in una singola configurazione NIC](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Passaggi successivi

Selezionare uno [scenario di distribuzione](f5-aad-integration.md) e avviare l'implementazione.