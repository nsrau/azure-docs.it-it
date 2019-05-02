---
title: Azure Resource Manager Test Drive | Azure Marketplace
description: Compilare un test drive di Marketplace tramite Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Patrick .Butler
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 7665050dfc9a561f42fec00c40d0a40dfa5cc183
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941581"
---
# <a name="azure-resource-manager-test-drive"></a>Test drive di Azure Resource Manager

Questo articolo è per gli editori la cui offerta è presente in Azure Marketplace o che sono presenti su AppSource, ma desiderano compilare il test drive con solo le risorse di Azure.

Un modello Azure Resource Manager (Resource Manager) è un contenitore di test codificato delle risorse di Azure progettare migliore rappresentano la soluzione. Se non si ha familiarità con il modello di Resource Manager, leggere [informazioni sui modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) e [creazione di modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) per assicurarsi di conoscere come compilare e testare modelli personalizzati.

Il test drive accetta il modello di Resource Manager fornito e consente la distribuzione di tutte le risorse necessarie da tale modello di Resource Manager in un gruppo di risorse.

Se si sceglie di creare un test drive di Azure Resource Manager, i requisiti sono:

- Compilare, testare e quindi caricare il modello di Resource Manager del test drive.
- Configurare tutti i metadati e le impostazioni richiesti per abilitare il test drive.
- Ripubblicare l'offerta con test drive abilitato.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Come compilare un test drive di Azure Resource Manager

Ecco il processo per la creazione di un'unità del Test di Azure Resource Manager:

1. Progettare ai clienti di eseguire operazioni in un diagramma di flusso desiderato.
1. Definire le esperienze desiderate ai clienti di compilare.
1. In base a definizioni indicate sopra, decidere quali parti e le risorse sono necessarie per i clienti eseguire tale esperienza: ad esempio, D365 istanza o un sito Web con un database.
1. Compilare il progetto in locale e l'esperienza di test.
1. Creare un pacchetto l'esperienza nella distribuzione di un modello ARM e da qui:
    1. Definire quali parti delle risorse sono parametri di input.
    1. Quali sono le variabili di;
    1. Quale output viene assegnato per l'esperienza dei clienti.
1. Pubblicare, testare e passare alla fase operativa.

La parte più importante della compilazione di un test drive di Azure Resource Manager consiste nel definire quali scenari si desidera che i clienti sperimentino. Si tratta di un prodotto firewall e si desidera eseguire una demo di come gestire al meglio gli attacchi script injection? Si tratta di un prodotto di archiviazione e si desidera eseguire una demo di come la soluzione consente di comprimere i file in modo semplice e veloce?

Assicurarsi di dedicare una quantità sufficiente di tempo la valutazione di che cosa sono i modi migliori per mostrare il prodotto. In particolare per tutte le risorse necessarie sarebbe necessario, man mano che il modello di Resource Manager sufficientemente semplice creazione di pacchetti.

Per continuare con l'esempio del firewall in questione, l'architettura potrebbe richiedere un URL di IP pubblico per il servizio e un altro per il sito Web protetto dal firewall. Tutti gli indirizzi IP vengono distribuiti in una macchina virtuale e connessi a un gruppo di sicurezza di rete e un'interfaccia di rete.

Dopo aver progettato il pacchetto di risorse desiderato, include ora la scrittura e la compilazione del modello di Test Drive Resource Manager.

## <a name="writing-test-drive-resource-manager-templates"></a>Scrittura di modelli di Resource Manager del test drive

Il test drive esegue le distribuzioni in modalità completamente automatica e, per questo motivo, i modelli di test drive presentano alcune restrizioni descritte di seguito.

### <a name="parameters"></a>Parametri

La maggior parte dei modelli dispone di un set di parametri. I parametri permettono di definire i nomi delle risorse, le dimensioni delle risorse (ad esempio, tipi di account di archiviazione o dimensioni delle macchine virtuali), nomi e password degli utenti, i nomi DNS e così via. Quando si distribuiscono soluzioni usando il portale di Azure, è possibile popolare manualmente tutti questi parametri, selezionare i nomi DNS o i nomi degli account di archiviazione disponibili e così via.

![Elenco dei parametri in Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Tuttavia, il test drive funziona in modalità interamente automatica, senza interazione umana, quindi supporta solo un set limitato di categorie di parametro. Se un parametro nel modello di Resource Manager del test drive non rientra in una delle categorie supportate, è necessario **sostituire questo parametro con un valore variabile o costante.**

È possibile usare qualsiasi nome valido per i parametri, test drive riconosce la categoria del parametro usando il valore del tipo di metadati. È **necessario specificare il tipo di metadati per ogni parametro di modello**, in caso contrario il modello non supererà la convalida:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

È importante notare che **tutti i parametri sono facoltativi**, quindi se non si desidera usare alcun parametro, non è necessario specificarlo.

### <a name="accepted-parameter-metadata-types"></a>Tipi di metadati di parametro accettati

| Tipo di metadati   | Tipo di parametro  | DESCRIZIONE     | Valore di esempio    |
|---|---|---|---|
| **baseuri**     | string          | URI di base del pacchetto di distribuzione| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **username**    | string          | Nuovo nome utente casuale.| admin68876      |
| **password**    | stringa sicura    | Nuova password casuale | Lp!ACS\^2kh     |
| **id sessione**   | string          | ID univoco della sessione di test drive (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>username

Test drive inizializza questo parametro con un **Uri di Base** del pacchetto di distribuzione, in modo che sia possibile usare il parametro per costruire l'Uri di un file incluso nel pacchetto.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

All'interno del modello, è possibile usare questo parametro per costruire l'Uri di tutti i file del pacchetto di distribuzione di test drive. L'esempio seguente illustra come costruire l'Uri del modello collegato:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

Test drive inizializza questo parametro con un nuovo nome utente casuale:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Valore di esempio:

    admin68876

È possibile usare i nomi utente casuali o costanti per la soluzione.

#### <a name="password"></a>password

Test drive inizializza questo parametro con una nuova password casuale:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Valore di esempio:

    Lp!ACS^2kh

È possibile usare password casuali o costanti per la soluzione.

#### <a name="session-id"></a>ID sessione

Test drive inizializzare questo parametro con un GUID univoco che rappresenta l'ID sessione di test drive:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Valore di esempio:

    b8c8693e-5673-449c-badd-257a405a6dee

È possibile utilizzare questo parametro per identificare in modo univoco la sessione di test drive, se necessario.

### <a name="unique-names"></a>Nomi univoci

Alcune risorse di Azure, ad esempio gli account di archiviazione o i nomi DNS, richiedono nomi univoci a livello internazionale.

Ciò significa che ogni volta che il test drive consente di distribuire il modello di Resource Manager, si crea un **nuovo gruppo di risorse con nome univoco** per tutte le\' relative risorse. Di conseguenza, è necessario usare la funzione [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) concatenata con i nomi delle variabili negli ID dei gruppi di risorse per generare valori univoci casuali:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Assicurarsi di concatenare le stringhe di parametri/variabili (\'contosovm\') con l'output di stringa univoca (\'resourceGroup ().id\'), perché questo garantisce univocità e affidabilità per ogni variabile.

Ad esempio, la maggior parte dei nomi di risorse non può iniziare con una cifra, ma la funzione di stringa univoca può restituire una stringa che inizia con una cifra. Pertanto, se si usa l'output di stringa univoca non elaborata, le distribuzioni avranno esito negativo. 

È possibile trovare altre informazioni sulle regole di denominazione delle risorse e le restrizioni in [questo articolo](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Percorso di distribuzione

È possibile rendere il test drive disponibile in diverse aree di Azure. Lo scopo è quello di consentire all'utente di selezionare l'area più vicina, per offrire la migliore esperienza utente.

Quando il test drive consente di creare un'istanza del lab, si crea sempre un gruppo di risorse nell'area scelta dall'utente e quindi si esegue il modello di distribuzione in questo contesto di gruppo. Pertanto, il modello deve selezionare il percorso di distribuzione dal gruppo di risorse:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Usare quindi questo percorso per tutte le risorse per un'istanza del lab specifica:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

È necessario assicurarsi che la sottoscrizione abbia il permesso di distribuire tutte le risorse che si desiderano in ognuna delle aree selezionate. È necessario inoltre assicurarsi che le immagini di macchina virtuale siano disponibili in tutte le aree che si desidera abilitare, in caso contrario il modello di distribuzione non funzionerà per alcune aree.

### <a name="outputs"></a>Output

In genere con i modelli di Resource Manager è possibile distribuire senza produrre un output. Infatti, si conoscono tutti i valori usati per popolare i parametri del modello ed è sempre possibile esaminare manualmente le proprietà di una risorsa.

Tuttavia, per i modelli di Resource Manager del test drive, è importante restituire al test drive tutte le informazioni necessarie per accedere al lab (gli URI del sito Web, i nomi host della macchina virtuale, i nomi utente e le password). Assicurarsi che tutti i nomi di output siano leggibili in quanto queste variabili vengono presentate al cliente.

Non esistono restrizioni relative agli output del modello. È importante ricordare che il test drive converte tutti i valori in di output in **stringhe**, pertanto se si invia un oggetto nell'output, un utente visualizzerà la stringa JSON.

Esempio:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni

Un ulteriore aspetto da tenere in considerazione sono la sottoscrizione e i limiti del servizio. Ad esempio, se si desidera distribuire fino a dieci macchine virtuali a 4 core, è necessario assicurarsi che la sottoscrizione usata per il lab consenta di utilizzare 40 core.

È possibile trovare altre informazioni sulla sottoscrizione di Azure e sui limiti dei servizio in [questo articolo](https://docs.microsoft.com/azure/azure-subscription-service-limits). Poiché è possibile eseguire più test drive allo stesso tempo, verificare che la sottoscrizione possa gestire il numero di core moltiplicati per il numero totale di test drive simultanei che possono essere eseguiti.

### <a name="what-to-upload"></a>Elementi da caricare

Il modello di Resource Manager del test drive viene caricato come un file con estensione zip, che può includere vari artefatti di distribuzione, ma deve disporre di un file denominato **main-template. JSON**. Questo file è il modello di distribuzione di Azure Resource Manager usato dal test drive per creare un'istanza del lab.

Se si dispone di risorse aggiuntive oltre a questo file, è possibile farvi riferimento come una risorsa esterna all'interno del modello, oppure è possibile includere la risorsa nel file zip.

Durante la certificazione di pubblicazione, il test drive decomprime il pacchetto di distribuzione e inserisce il contenuto in un contenitore BLOB di test drive interno. La struttura del contenitore riflette la struttura del pacchetto di distribuzione:

| package.zip                       | Contenitore BLOB di test drive         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| templates/solution.json           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1  |


Si definisce Uri di base un Uri del contenitore BLOB. Ogni revisione del lab ha il proprio contenitore BLOB, di conseguenza ogni revisione del lab ha il proprio Uri di base. Il test drive permette di trasferire un URI di base del pacchetto di distribuzione non compresso nel modello tramite i parametri del modello.

## <a name="transforming-template-examples-for-test-drive"></a>Esempi di modelli di trasformazione per test drive

Il processo di trasformazione di un'architettura di risorse in un modello di Resource Manager del test drive può risultare scoraggiante. Per semplificare questo processo, si riportano esempi su come [trasformare al meglio le architetture più recenti qui](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Come pubblicare un test drive

Dopo aver compilato il test drive, questa sezione descrive i campi necessari per pubblicare correttamente il test drive.

![Abilitazione del test drive nell'interfaccia utente](./media/azure-resource-manager-test-drive/howtopub1.png)

Il primo e più importante campo è per attivare o disattivare il test drive per l'offerta. Selezionando **Sì,** si visualizza il resto del modulo con tutti i campi obbligatori da compilare. Selezionando **No,** il modulo viene disabilitato e se si pubblica nuovamente con il test drive disabilitato, il test drive viene rimosso dall'ambiente di produzione.

Note: se non ci sono test drive usati attivamente dagli utenti, quei test drive continuano l'esecuzione fino alla scadenza della sessione.

### <a name="details"></a>Dettagli

La sezione successiva da compilare contiene i dettagli relativi all'offerta del test drive.

![Informazioni dettagliate sul test drive](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrizione -** *Campo obbligatorio* Il campo in cui scrivere la descrizione principale di ciò che è presente nel test drive. Il cliente potrà scoprire gli scenari relativi al prodotto ricoperti dal test drive. 

**Manuale dell'utente -** *Campo obbligatorio* Tratta approfonditamente la procedura guidata dell'esperienza di test drive. Una volta aperto, il cliente verrà guidato a compiere i passaggi richiesti attraverso il test drive. È importante che il contenuto sia facile da comprendere e seguire. (Deve essere un file con estensione PDF)

**Video demo del test drive** *Consigliato* Come per il Manuale per l'utente, la cosa migliore è inserire un tutorial video dell'esperienza di test drive. Il cliente lo vedrà prima o durante il test drive e potrà compiere i passaggi a lui richiesti attraverso il test drive. È importante che il contenuto sia facile da comprendere e seguire.

- **Nome** -Titolo del video
- **Collegamento** - Deve essere un URL incorporato di YouTube o Vimeo. Di seguito, un esempio di come recuperare l'URL incorporato:
- **Anteprima** Deve essere un'immagine di alta qualità (533x324 pixel). È consigliabile acquisire una schermata di alcune parti dell'esperienza di test drive.

Di seguito è riportato come questi campi vengono visualizzati dai clienti durante l'esperienza di test drive.

![Posizione dei campi del test drive nell'offerta di Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configurazione tecnica

Nella successiva sezione da compilare, si carica il modello di Resource Manager del test drive e si definisce il grado di specificità delle istanze del test drive.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Istanze -** *Campo obbligatorio* Qui è possibile configurare il numero di istanze desiderate, in quali aree e la velocità con cui i clienti possono ottenere il test drive.

- **Istanze** - Le aree selezionate indicano dove si sceglie la posizione in cui il modello di Resource Manager del test drive viene distribuito. È consigliabile selezionare una sola area in cui si prevede che clienti siano posizionati.
- **Accesso frequente** - Il numero di istanze di test drive che sono già distribuite e in attesa dell'accesso per ogni area selezionata. I clienti possono accedere immediatamente a questo test drive, anziché dover attendere la distribuzione. Il compromesso è che queste istanze sono sempre in esecuzione nella sottoscrizione di Azure, comportando quindi un costo relativo al tempo di attività più elevato. È consigliabile avere **almeno un'istanza ad accesso frequente**, in quanto la maggior parte dei clienti non desidera attendere il termine delle distribuzioni complete e pertanto si verifica un calo dell'utilizzo dei clienti.
- **Accesso medio** - Il numero di istanze di test drive per area che sono stati distribuiti con conseguente arresto della macchina virtuale e archiviazione di quest'ultima in Azure. Il tempo di attesa per le istanze ad accesso medio è superiore rispetto a quello delle istanze ad accesso frequente, ma il costo del tempo di attività di archiviazione è inferiore.
- **Accesso sporadico** - Il numero di istanze di test drive per ogni area che possono essere distribuite. Le istanze ad accesso sporadico richiedono che l'intero modello di Resource Manager del test drive sia distribuito nel momento in cui un cliente richiede il test drive, pertanto sono più lente rispetto alle istanze ad accesso frequente o medio. Tuttavia, il compromesso è che è necessario pagare solo per la durata del test drive.

A questo punto si calcola il numero totale di potenziali test drive simultanei che si renderanno disponibili e si verifica che il limite di quota per la sottoscrizione possa gestire tale quantità:

**(Numero di aree selezionate x Istanze ad accesso frequente) + (Numero di aree selezionate x Istanze ad accesso medio) + (Numero di aree selezionate x Istanze ad accesso sporadico)**

**Durata del test drive (ore)** *Campo obbligatorio* Durata del tempo in cui il test drive rimane attivo, in numero di ore. Il test drive termina automaticamente alla fine di questo periodo di tempo.

**Modello di Resource Manager del test drive -** *Campo obbligatorio* Caricare il modello di Resource Manager di seguito. Si tratta del file creato nella sezione precedente. Assegnare un nome al file del modello principale: "main-template. JSON" e assicurarsi che il modello di Resource Manager contenga i parametri di output per le variabili di chiave necessarie. (Deve essere un file con estensione zip)

**Accedere alle informazioni -** *Campo obbligatorio* Dopo che un cliente ha ottenuto il test drive, vengono visualizzate le informazioni di accesso. Queste istruzioni sono pensate per condividere i parametri di output utili dal modello di Resource Manager del test drive. Per includere i parametri di output, usare le parentesi graffe doppie (ad esempio, **{{nomeoutput}}**) e questi verranno inseriti correttamente nella posizione. (La formattazione delle stringhe HTML è consigliata qui per eseguire il rendering nel front-end).

### <a name="test-drive-deployment-subscription-details"></a>Dettagli di sottoscrizione della distribuzione del test drive

L'ultima sezione da compilare consente di distribuire automaticamente i test drive connettendo la sottoscrizione di Azure e Azure Active Directory (AD).

![Dettagli di sottoscrizione della distribuzione del test drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID di sottoscrizione di Azure -** *Campo obbligatorio* Si concede l'accesso ai servizi di Azure e al portale di Azure. Nella sottoscrizione viene tenuto traccia dell'uso delle risorse e i servizi vengono fatturati. Se non si dispone ancora di una sottoscrizione **separata** di Azure solo per test drive, proseguire e crearne una. Per trovare gli ID di sottoscrizione di Azure, registrarsi sul portale di Azure e andare nella sezione delle sottoscrizioni nella parte in basso a sinistra del menu. (Esempio: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Sottoscrizioni di Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID del tenant di Azure AD -** *Campo obbligatorio* È possibile trovare l'ID del tenant già disponibile di seguito in Proprietà -\> ID directory.

![Proprietà di Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

In caso contrario, creare un nuovo tenant in Azure Active Directory.

![Elenco dei tenant di Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definire l'organizzazione, il dominio e il paese del tenant di Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Confermare la selezione](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID dell'app Azure AD -** *Campo obbligatorio* Il passaggio successivo consente di creare e registrare una nuova applicazione. Si userà questa applicazione per eseguire operazioni nell'istanza di test drive.

1. Nella directory appena creata o in quelle già esistenti, selezionare la directory Azure Active nel filtro.
2. Cercare "Registrazioni app" e fare clic su "Aggiungi"
3. Immettere un nome applicazione.
4. Selezionare il tipo come "app/API Web"
5. Fornire qualsiasi valore nell'URL di accesso, quel campo non verrà utilizzato.
6. Fare clic su Crea.
7. Dopo aver creato l'applicazione, andare su Proprietà \> Impostare l'applicazione come multi tenant e fare clic su Salva.

Fare clic su Save. L'ultimo passaggio consiste nell'individuare l'ID applicazione per l'app registrata e nell'incollarlo nel campo del test drive qui di seguito.

![Dettaglio di ID applicazione AD Azure](./media/azure-resource-manager-test-drive/subdetails7.png)

Poiché l'applicazione da distribuire nella sottoscrizione è in uso, è necessario aggiungere l'applicazione come collaboratore nella sottoscrizione. Le istruzioni per questi sono le seguenti:

1. Sul pannello di sottoscrizione, selezionare la sottoscrizione in uso usando soltanto per il test drive.
1. Fare clic su **Controllo di accesso (IAM)**.
1. Fare clic sulla scheda **Assegnazioni di ruolo**.  ![Aggiungere una nuova entità di sicurezza controllo di accesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Fare clic su **Aggiungi assegnazione ruolo**.
1. Impostare il ruolo di **collaboratore**.
1. Digitare il nome dell'applicazione Azure AD e selezionare l'applicazione per assegnare il ruolo.
    ![Aggiungere le autorizzazioni](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Fare clic su **Save**.

**Chiave dell'app Azure AD -** *Campo obbligatorio* L'ultimo campo genera una chiave di autenticazione. In Chiavi, aggiungere una descrizione di chiave, impostare la scadenza come Non scade, quindi selezionare Salva. È **importante** evitare di avere una chiave scaduta, che interrompe il test drive in corso. Copiare questo valore e incollarlo nel campo corrispondente del test drive.

![Mostra le chiavi per l'applicazione Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver compilato tutti i campi del test drive, proseguire e **Ripubblicare** l'offerta. Dopo che il test drive ha superato la certificazione, è possibile effettuare un test completo dell'esperienza del cliente nell'**anteprima** dell'offerta. Avviare un test drive nell'interfaccia utente, quindi aprire la sottoscrizione di Azure nel portale di Azure e verificare che i test drive siano stati distribuiti correttamente.

![Portale di Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

È importante notare che non è possibile eliminare istanze di test drive poiché ne viene effettuato il provisioning per i clienti, quindi il servizio di test drive pulirà automaticamente i gruppi di risorse dopo che il cliente avrà terminato l'operazione.

Una volta acquisita familiarità con l'offerta di anteprima, è il momento di **passare alla fase operativa**. Una volta che l'offerta è stata pubblicata, Microsoft provvederà a una revisione finale per controllare l'intera esperienza end-to-end. Se per qualche motivo l'offerta viene rifiutata, verrà inviata una notifica al contatto tecnico relativa all'offerta che spiegherà le problematiche da risolvere.

In caso di domande, richieste di suggerimenti per la risoluzione dei problemi o per rendere più efficiente il test drive, visitare [Domande frequenti, risoluzione dei problemi e procedure consigliate](./marketing-and-best-practices.md).
