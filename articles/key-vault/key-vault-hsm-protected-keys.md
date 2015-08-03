<properties
	pageTitle="Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure | Microsoft Aure"
	description="Questo argomento permette di pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="07/22/2015"
	ms.author="cabailey"/>
#Come generare e trasferire chiavi HSM protette per l'insieme di credenziali delle chiavi di Azure

##Introduzione

Per una maggiore sicurezza, quando si usa l'insieme di credenziali delle chiavi di Azure è possibile importare o generare le chiavi in moduli di protezione hardware (HSM) che rimangono sempre entro il limite HSM. Questo scenario viene spesso definito con il termine modalità *Bring Your Own Key* o BYOK. I moduli di protezione hardware sono certificati per FIPS 140-2 livello 2. L'insieme di credenziali delle chiavi di Azure usa la famiglia di HSM nShield di Thales per proteggere le chiavi.

Questo argomento include informazioni utili per pianificare, generare e quindi trasferire le proprie chiavi HSM protette da usare con l'insieme di credenziali delle chiavi di Azure.

>[AZURE.NOTE]Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](key-vault-whatis.md)
>
>Per un'esercitazione introduttiva che illustra la creazione di un insieme di credenziali delle chiavi per chiavi HSM protette, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md).

Altre informazioni su generazione e trasferimento di una chiave HSM protetta tramite Internet:

- La chiave viene generata in una workstation offline per ridurre la superficie di attacco.

- La crittografia della chiave viene eseguita tramite una chiave per lo scambio delle chiavi che rimane crittografata fino al momento del trasferimento ai moduli di protezione hardware dell'insieme di credenziali delle chiavi di Azure. Solo la versione crittografata della chiave viene inviata dalla workstation originale.

- Il set di strumenti imposta proprietà sulla chiave che consentono di associarla all'ambiente di sicurezza dell'insieme di credenziali delle chiavi di Azure. Di conseguenza, dopo che i moduli di protezione hardware dell'insieme di credenziali delle chiavi di Azure avranno ricevuto e decrittografato la chiave, saranno gli unici componenti a poterla usare. La chiave non può essere esportata. Questo binding viene applicato dai moduli di protezione hardware Thales.

- La chiave per lo scambio delle chiavi usata per crittografare la chiave viene generata nei moduli di protezione hardware dell'insieme di credenziali delle chiavi di Azure e non è esportabile. I moduli di protezione hardware applicano la regola in base alla quale non può esistere una versione non crittografata della chiave per lo scambio delle chiavi all'esterno dei moduli stessi. Il set di strumenti include inoltre un'attestazione di Thales che dichiara che la chiave per lo scambio delle chiavi non è esportabile e che è stata generata in un modulo di protezione hardware originale prodotto da Thales.

- Il set di strumenti include un'attestazione di Thales che dichiara che anche l'ambiente di sicurezza dell'insieme di credenziali delle chiavi di Azure è stato generato in un modulo di protezione hardware originale prodotto da Thales. In questo modo l'utente ha la conferma che Microsoft usa hardware originale.

- Microsoft usa chiavi per lo scambio delle chiavi e ambienti di sicurezza separati in ogni area geografica per garantire che sia possibile usare la chiave solo nei data center presenti nell'area geografica in cui è stata generata. Una chiave di un cliente europeo, ad esempio, non può essere usata in data center che si trovano in America del Nord o in Asia.

##Altre informazioni sui moduli di protezione hardware di Thales e sui servizi di Microsoft

Thales e-Security è un fornitore leader a livello mondiale di soluzioni di crittografia dei dati e di sicurezza informatica per i settori finanziario, tecnologico, manifatturiero e pubblico. Basate su un'esperienza di 40 anni nella protezione di informazioni aziendali e degli enti pubblici, le soluzioni Thales vengono usate da quattro delle cinque maggiori società dei settori energetico e aerospaziale, in 22 paesi NATO, e consentono di proteggere più dell'80 percento delle transazioni di pagamento in tutto il mondo.

Microsoft ha collaborato con Thales per migliorare il livello tecnologico dei moduli di protezione hardware per consentire all'utente di sfruttare i vantaggi tipici dei servizi ospitati senza perdere il controllo sulle proprie chiavi. In particolare, tali miglioramenti consentono a Microsoft di gestire i moduli di protezione hardware in modo che questa operazione non debba essere eseguita dall'utente. In quanto servizio cloud, l'insieme di credenziali delle chiavi di Azure è in grado di supportare la scalabilità verticale con breve preavviso per soddisfare i picchi d'uso dell'organizzazione. Contemporaneamente, la chiave è protetta all'interno dei moduli di protezione hardware di Microsoft e l'utente mantiene il controllo sul ciclo di vita della chiave, perché genera la chiave e la trasferisce ai moduli di protezione hardware di Microsoft.

##Implementazione di BYOK (Bring Your Own Key) per l'insieme di credenziali delle chiavi di Azure

Usare le informazioni e le procedure seguenti se si genera una chiave HSM protetta e quindi la si trasferisce all'insieme di credenziali delle chiavi di Azure, ovvero lo scenario BYOK (Bring Your Own Key).


##Prerequisiti per la modalità BYOK

Nella tabella seguente sono elencati i prerequisiti relativi alla modalità BYOK per l'insieme di credenziali delle chiavi di Azure.

|Requisito|Altre informazioni|
|---|---|
|Sottoscrizione di Azure|Per creare un insieme di credenziali delle chiavi di Azure, è necessaria una sottoscrizione di Azure: [Iscriversi per una versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/)|
|insieme di credenziali delle chiavi di Azure che supporta moduli di protezione hardware|Per altre informazioni su livelli di servizio e funzionalità per l'insieme di credenziali delle chiavi di Azure, vedere il sito Web relativo ai [prezzi dell'insieme di credenziali delle chiavi di Azure](http://azure.microsoft.com/pricing/details/key-vault/).|
|Moduli di protezione hardware Thales, smart card e software di supporto|È necessario avere l'accesso ai moduli di protezione hardware Thales e averne una conoscenza a livello operativo. Per l'elenco dei modelli compatibili o per acquistare un modulo di protezione hardware qualora non se ne sia già in possesso, vedere la pagina relativa ai [moduli di protezione hardware Thales](https://www.thales-esecurity.com/msrms/buy).|
|Componenti hardware e software seguenti:<ol><li>Workstation x64 offline con sistema operativo Windows 7 o versioni successive e software nShield di Thales versione 11.50 o successive.<br/><br/>Se nella workstation è in esecuzione Windows 7, sarà necessario [installare Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Workstation connessa a Internet con sistema operativo Windows 7 o versioni successive.</li><li>Unità USB o altro dispositivo di archiviazione portatile con almeno 16 MB di spazio disponibile.</li></ol>|Per motivi di sicurezza, si consiglia che la prima workstation non sia connessa a una rete. Questa condizione tuttavia non viene applicata a livello di codice.<br/><br/>Nelle istruzioni seguenti a questa workstation si fa riferimento come workstation disconnessa.</p></blockquote><br/>Se inoltre la propria chiave del tenant è destinata a essere usata in una rete di produzione, si consiglia di usare una seconda workstation separata per scaricare il set di strumenti e caricare la chiave del tenant. A scopo di test è comunque possibile usare la prima workstation.<br/><br/>Nelle istruzioni seguenti alla seconda workstation si fa riferimento come workstation connessa a Internet.</p></blockquote><br/>|

##Generare e trasferire la chiave al modulo di protezione hardware dell'insieme di credenziali delle chiavi di Azure

Per generare e trasferire la chiave al modulo di protezione hardware dell'insieme di credenziali delle chiavi di Azure, eseguire i cinque passaggi seguenti:

- [Passaggio 1: Preparare la workstation connessa a Internet](#step-1-prepare-your-internet-connected-workstation)
- [Passaggio 2: Preparare la workstation disconnessa](#step-2-prepare-your-disconnected-workstation)
- [Passaggio 3: Generare la chiave](#step-3-generate-your-key)
- [Passaggio 4: Preparare la chiave per il trasferimento](#step-4-prepare-your-key-for-transfer)
- [Passaggio 5: Trasferire la chiave all'insieme di credenziali delle chiavi di Azure](#step-5-transfer-your-key-to-azure-key-vault)

## Passaggio 1: Preparare la workstation connessa a Internet
Per questo primo passaggio è necessario eseguire le procedure seguenti nella workstation connessa a Internet.


###Passaggio 1.1: Installare Azure PowerShell

Dalla workstation connessa a Internet scaricare e installare il modulo di Azure PowerShell che include i cmdlet per la gestione dell'insieme di credenziali delle chiavi di Azure. È necessaria la versione minima 0.8.13.

Per le istruzioni di installazione, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

###Passaggio 1.2: Ottenere l'ID sottoscrizione di Azure

Avviare una sessione di Azure PowerShell e accedere al proprio account Azure con il comando seguente:

		Add-AzureAccount
Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Usare quindi il comando [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx):

		Get-AzureSubscription
Nell'output individuare l'ID della sottoscrizione che si userà per l'insieme di credenziali delle chiavi di Azure. Questo ID sottoscrizione verrà usato in seguito.

Non chiudere la finestra di Azure PowerShell.

###Passaggio 1.3: Scaricare il set di strumenti BYOK per l'insieme di credenziali delle chiavi di Azure

Accedere all'Area download Microsoft e [scaricare il set di strumenti BYOK per l'insieme di credenziali delle chiavi di Azure](http://www.microsoft.com/download/details.aspx?id=45345) per la propria area:

|Area|Nome del pacchetto|Hash del pacchetto SHA-256|
|---|---|---|
|America del Nord|KeyVault-BYOK-Tools-UnitedStates.zip|D9FDA9F5A34E1388CD6C9138E5B75B7051FB7D6B11F087AFE0553DC85CCF0E36|
|Europa|KeyVault-BYOK-Tools-Europe.zip|881DCA798305B8408C06BAE7B3EFBC1E9EA6113A8D6EC443464F3744896F32C3|
|Asia|KeyVault-BYOK-Tools-AsiaPacific.zip|0C76967B3AC76687E4EA47EB96174EE6B25AB24E3114E28A90D9B93A2E6ABF6E|
|America Latina|KeyVault-BYOK-Tools-LatinAmerica.zip|B38015990D4D1E522B8367FF78E78E0234BF9592663470426088C44C3CAAAF48|

Per convalidare l'integrità del set di strumenti BYOK, nella sessione di Azure PowerShell usare il cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx).

	Get-FileHash KeyVault-BYOK-Tools-*.zip

Il set di strumenti include gli elementi seguenti:

- Pacchetto di chiavi per lo scambio di chiavi con un nome che inizia con **BYOK-KEK-pkg-.**
- Pacchetto relativo all'ambiente di sicurezza con un nome che inizia con **BYOK-SecurityWorld-pkg-.**
- Script python denominato v**erifykeypackage.py**.
- File eseguibile dalla riga di comando denominato **KeyTransferRemote.exe** e DLL associate.
- Componente Visual C++ Redistributable Package denominato **vcredist_x64.exe.**

Copiare il pacchetto in un'unità USB o in un altro dispositivo di archiviazione portatile.

##Passaggio 2: Preparare la workstation disconnessa

Per questo secondo passaggio eseguire le procedure seguenti nella workstation non connessa alla rete (Internet o la rete interna).


###Passaggio 2.1: Preparare la workstation disconnessa con il modulo di protezione hardware Thales

Installare il software di supporto nCipher (Thales) in un computer Windows, quindi collegare un modulo di protezione hardware Thales a tale computer.

Verificare che gli strumenti Thales si trovino nel percorso locale (**%nfast_home%\bin** e **%nfast_home%\python\bin**). Digitare ad esempio:

		set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Per altre informazioni, vedere il manuale dell'utente fornito con il modulo di protezione hardware Thales.

###Passaggio 2.2: Installare il set di strumenti BYOK nella workstation disconnessa

Copiare il pacchetto del set di strumenti BYOK dall'unità USB o da un altro dispositivo di archiviazione portatile, quindi eseguire le operazioni seguenti:

1. Estrarre i file dal pacchetto scaricato in una cartella qualsiasi.
2. In tale cartella eseguire vcredist_x64.exe.
3. Seguire le istruzioni per installare i componenti di runtime di Visual C++ per Visual Studio 2012.

##Passaggio 3: Generare la chiave

Per questo terzo passaggio eseguire le procedure seguenti nella workstation disconnessa.

###Passaggio 3.1: Creare un ambiente di sicurezza

Avviare un prompt dei comandi ed eseguire il programma new-world di Thales.

	new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Questo programma crea un file di **ambiente di sicurezza** nel percorso %NFAST_KMDATA%\local\world, che corrisponde alla cartella C:\ProgramData\nCipher\Key Management Data\local. È possibile creare valori diversi per il quorum, ma nell'esempio viene chiesto di immettere tre schede vuote e un codice PIN per ogni scheda. Qualsiasi coppia di schede consentirà di accedere in modo completo all'ambiente di sicurezza. Queste schede diventano il **set di schede amministrative** per il nuovo ambiente di sicurezza.

Eseguire quindi le operazioni seguenti:

- Eseguire il backup del file relativo all'ambiente. Proteggere il file relativo all'ambiente, le schede amministrative e i codici PIN relativi e verificare che nessuna singola persona possa accedere a più di una scheda.

###Passaggio 3.2: Convalidare il pacchetto scaricato

Questo passaggio è facoltativo, ma è consigliato in modo che sia possibile convalidare gli elementi seguenti:

- Chiave per lo scambio delle chiavi inclusa nel set di strumenti generato da un modulo di protezione hardware Thales originale.
- Hash dell'ambiente di sicurezza incluso nel set di strumenti generato da un modulo di protezione hardware Thales originale.
- Impossibilità di esportare la chiave per lo scambio delle chiavi.

>[AZURE.NOTE]Per convalidare il pacchetto scaricato, il modulo di protezione hardware deve essere connesso e acceso e deve essere associato a un ambiente di sicurezza (ad esempio quello appena creato).

Per convalidare il pacchetto scaricato:

1.	Eseguire lo script verifykeypackage.py associando uno dei comandi seguenti, a seconda dell'area geografica di appartenenza:
	- America del Nord

			python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
	- Europa

			python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
	- Asia

			python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
	- America Latina

			python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
	- Giappone

			python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1

	>[AZURE.TIP]Il software Thales include python nel percorso %NFAST_HOME%\python\bin.
	
2.	Assicurarsi di visualizzare il risultato positivo seguente, che indica il completamento della convalida: **Result: SUCCESS**

Questo script consente di convalidare la catena di firmatari fino alla chiave radice di Thales. La funzione hash di questa chiave radice è incorporata nello script e il relativo valore deve essere **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Si può anche confermare questo valore separatamente sul [sito Web di Thales](http://www.thalesesec.com/).

È ora possibile creare una nuova chiave.

###Passaggio 3.3: Creare una nuova chiave

Generare una chiave tramite il programma **generatekey** di Thales.

Eseguire il comando seguente per generare la chiave:

	generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando si esegue il comando, usare le istruzioni seguenti:

- Sostituire il valore di *contosokey* per gli elementi **ident** e **plainname** con qualsiasi valore di stringa. Per ridurre il sovraccarico amministrativo e il rischio di errori, è consigliabile usare lo stesso valore per entrambi gli elementi. Il valore **ident** deve contenere solo numeri, trattini e lettere minuscole

- L'elemento pubexp viene lasciato vuoto in questo esempio (impostazione predefinita), ma è possibile indicare valori specifici. Per altre informazioni, vedere la documentazione di Thales.

Questo comando crea un file di chiave in formato token nella cartella %NFAST_KMDATA%\local con un nome che inizia con **key_simple_** seguito dall'elemento ident specificato nel comando. Ad esempio: **key_simple_contosokey**. Questo file contiene una chiave crittografata.

Eseguire il backup del file di chiave in formato token in un percorso sicuro.

>[AZURE.IMPORTANT]Quando in seguito si trasferisce la chiave all'insieme di credenziali delle chiavi di Azure, Microsoft non può esportarla nuovamente nei dispositivi dell'utente, quindi è estremamente importante eseguire il backup della chiave e dell'ambiente di sicurezza in modo sicuro. Per ottenere informazioni aggiuntive e procedure consigliate per eseguire il backup della chiave, contattare Thales.

È ora possibile trasferire la chiave all'insieme di credenziali delle chiavi di Azure.

##Passaggio 4: Preparare la chiave per il trasferimento

Per questo quarto passaggio eseguire le procedure seguenti nella workstation disconnessa.

###Passaggio 4.1: Creare una copia della chiave con autorizzazioni ridotte

Per ridurre le autorizzazioni sulla chiave, in un prompt dei comandi eseguire uno dei comandi seguenti in base alla propria area geografica:

- America del Nord

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Europa

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Asia

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- America Latina

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Giappone

		KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1

Quando si esegue il comando, sostituire *contosokey* con lo stesso valore specificato in **Passaggio 3.3: Creare una nuova chiave** nel passaggio [Generare la chiave](#step-3-generate-your-key).

Viene chiesto di inserire le schede amministrative relative all'ambiente di sicurezza.

Al completamento del comando, viene visualizzato il messaggio **Result: SUCCESS** e la copia della chiave con autorizzazioni ridotte si troverà nel file denominato key_xferacId_<contosokey>.

###Passaggio 4.2: Verificare la nuova copia della chiave

Facoltativamente, eseguire le utilità Thales per verificare le autorizzazioni minime sulla nuova chiave:

- aclprint.py:

		"%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

		"%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Quando si esegue il comando, sostituire contosokey con lo stesso valore specificato in **Passaggio 3.3: Creare una nuova chiave** nel passaggio [Generare la chiave](#step-3-generate-your-key).

###Passaggio 4.3: Crittografare la chiave tramite la chiave per lo scambio di chiavi di Microsoft

Eseguire uno dei comandi seguenti, a seconda dell'area geografica di appartenenza:

- America del Nord

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Europa

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Asia

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- America Latina

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Giappone

		KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Quando si esegue il comando, usare le istruzioni seguenti:

- Sostituire *contosokey* con l'identificatore usato per generare la chiave in **Passaggio 3.3: Creare una nuova chiave** nel passaggio [Generare la chiave](#step-3-generate-your-key).

- Sostituire *SubscriptionID* con l'ID della sottoscrizione di Azure che contiene l'insieme di credenziali delle chiavi Questo valore è stato recuperato in precedenza in **Passaggio 1.2: Ottenere l'ID sottoscrizione di Azure** dal passaggio [Preparare la workstation connessa a Internet](#step-1-prepare-your-internet-connected-workstation).

- Sostituire *ContosoFirstHSMKey* con un'etichetta che verrà usata per il nome del file di output.

Se l'operazione ha esito positivo, viene visualizzato il messaggio **Result: SUCCESS** e nella cartella corrente sarà presente un nuovo file con il nome TransferPackage-*ContosoFirstHSMkey*.byok.

###Passaggio 4.4: Copiare il pacchetto di trasferimento della chiave nella workstation connessa a Internet 

Usare un'unità USB o un altro dispositivo di archiviazione portatile per copiare il file di output creato nel passaggio precedente (KeyTransferPackage-ContosoFirstHSMkey.byok) nella workstation connessa a Internet.

##Passaggio 5: Trasferire la chiave all'insieme di credenziali delle chiavi di Azure

Per questo passaggio finale, nella workstation connessa a Internet usare il cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048.aspx) per caricare il pacchetto di trasferimento della chiave copiato dalla workstation disconnessa al modulo di protezione hardware dell'insieme di credenziali delle chiavi di Azure:

	Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se il pacchetto viene caricato correttamente, verranno visualizzate le proprietà della chiave aggiunta.

##Passaggi successivi

È ora possibile usare questa chiave HSM protetta nell'insieme di credenziali delle chiavi. Per altre informazioni, vedere la sezione **Per usare un modulo di protezione hardware** nell'esercitazione [Introduzione all'insieme di credenziali delle chiavi di Azure](key-vault-get-started.md).

<!---HONumber=July15_HO4-->