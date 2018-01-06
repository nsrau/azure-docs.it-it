
 
La diffusione di recente un [nuova classe di vulnerabilità CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) noto come attacchi al canale laterale speculativo esecuzione ha comportato domande dei clienti che desiderano ottenere maggiore chiarezza. 

 
## <a name="azure-infrastructure"></a>Infrastruttura di Azure

I problemi descritti nella divulgazione delle vulnerabilità può essere utilizzati per ignorare un limite di hypervisor e consentire la diffusione di memoria tra due macchine virtuali host condivisi. Come indicato nella versione precedente [post di blog](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/), Azure ha applicato le misure di attenuazione per proteggere i clienti contro questa vulnerabilità.  Microsoft consiglia sempre di applicare le procedure consigliate per le immagini di macchina virtuale, inclusa l'installazione di tutti gli aggiornamenti di sicurezza dal proprio fornitore del sistema operativo.

## <a name="paas-services-on-azure"></a>Servizi PaaS in Azure
Offerte PaaS di Azure sono soluzioni distribuite per impostazione predefinita. È richiesta alcuna azione da parte dei clienti. Per l'eccezione di servizi Cloud di Azure, vedere di seguito.  


## <a name="azure-cloud-services"></a>Servizi cloud di Azure

[Servizi Cloud di Azure](https://azure.microsoft.com/services/cloud-services/) con aggiornamento automatico attivato automaticamente riceve una versione del sistema operativo Guest che include le misure di attenuazione per queste vulnerabilità. 

Le seguenti versioni del sistema operativo Guest includono gli aggiornamenti con protezione contro le vulnerabilità di canale lato speculativo esecuzione:

* WA-GUEST-OS-5.15_201801-01
* WA-GUEST-OS-4.50_201801-01


Inoltre è consigliabile abilitare un numero ridotto di clienti che utilizzano i servizi Cloud di Azure per ospitare il codice non attendibile [Shadowing di indirizzo virtuale Kernel](#enabling-kernel-virtual-address-shadowing-on-windows-server) oltre ad aggiornare il sistema operativo Guest. Questo fornisce protezione aggiuntiva contro l'esecuzione speculativo canale lato vulnerabilità. Questo può essere eseguito tramite un'attività di avvio. Altre informazioni sui clienti e gli scenari di utilizzo richiedono questa funzionalità e sulla relativa abilitazione, viene fornito di seguito.


## <a name="azure-virtual-machines-windows--linux"></a>Macchine virtuali di Azure (Windows e Linux)

Microsoft consiglia sempre di installare tutti gli aggiornamenti di sicurezza. 

Rollup della sicurezza di gennaio 2018 contiene correzioni per queste vulnerabilità e altro ancora. Verificare che si esegue un'applicazione antivirus supportata prima di installare gli aggiornamenti del sistema operativo. Per informazioni sulla compatibilità, contattare il fornitore di software antivirus. 

Per risolvere le vulnerabilità speculativo esecuzione, gli aggiornamenti a Linux kernel verrà richiesto e può essere ottenuto dal provider di distribuzione, se disponibile. 

Inoltre è consigliabile abilitare un numero ridotto di clienti che utilizzano macchine virtuali di Azure (Windows) per ospitare il codice non attendibile [Shadowing di indirizzo virtuale Kernel](#enabling-kernel-virtual-address-shadowing-on-windows-server) che fornisce protezione aggiuntiva contro esecuzione speculativo canale laterale vulnerabilità.  Altre informazioni sui quali clienti e l'utilizzo scenari richiedono questa funzionalità e sulla relativa abilitazione viene fornite di seguito.


## <a name="enabling-kernel-virtual-address-shadowing-on-windows-server"></a>Abilitazione del Kernel indirizzo virtuale Shadowing in Windows Server

I clienti che utilizzano Windows Server per eseguire codice non attendibile devono abilitare la funzione Kernel virtuale indirizzo Shadowing che fornisce protezione per i sistemi in cui è in esecuzione il codice non attendibile con privilegi limitati.

Questa protezione aggiuntiva può influire sulle prestazioni e viene disattivata per impostazione predefinita. Shadowing di indirizzo virtuale kernel protegge contro la diffusione di informazioni di processo a altro e kernel-in-process.

Macchine virtuali o servizi Cloud, server locali sono a rischio se che rientrano in una delle categorie seguenti:

* Azure annidati gli host di virtualizzazione Hyper-V
* Host (host sessione Desktop remoto) di Servizi Desktop remoto
* Macchine virtuali o servizi cloud che eseguono codice non attendibile, ad esempio contenitori o le estensioni non attendibili per i database, carichi di lavoro o contenuto web non attendibili che eseguire il codice che viene fornito da origini esterne.

Un esempio di uno scenario in cui è necessario lo Shadowing indirizzo Kernel virtuale: 

|     |
|-----|
|Una macchina virtuale di Azure viene eseguito un servizio in cui gli utenti non attendibili è possono inviare codice JavaScript che viene eseguito con privilegi limitati. Nella stessa macchina virtuale, è presente un processo con privilegi elevati che contiene dati riservati che non devono essere accessibili agli utenti. In questo caso, è necessario per fornire una protezione contro la divulgazione tra i due Shadowing di indirizzo virtuale del Kernel.|
|     | 

Sono disponibili tramite le istruzioni specifiche sull'abilitazione del Kernel virtuale indirizzo Shadowing [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution).


> [!NOTE]
> Al momento della pubblicazione, lo Shadowing indirizzo Kernel virtuale è disponibile solo in Windows Server 2016, Windows Server 2012 R2 e Windows Server 2008 R2.  
>
>

Se si esegue un Linux Server, consultare il fornitore del sistema dei sistemi operativi per gli aggiornamenti e le istruzioni.

## <a name="branch-target-injection-mitigation-support-microcode"></a>Attacco intrusivo nel codice di destinazione ramo, supporto attenuazione (microcodice)

I clienti che utilizzano gli strumenti che rilevano la presenza di soluzioni basate su microcodice report che Azure è senza patch. Non è corretto. Al momento della pubblicazione, il supporto di attenuazione ramo destinazione attacco intrusivo nel codice non è esposta dall'Hypervisor Azure in macchine virtuali di Azure o i servizi Cloud di Azure. Ciò significa che le macchine virtuali non sono consapevoli dell'esistenza del microcodice e non è possibile utilizzare il set di istruzioni aumentata. Ciò non significa che Azure è vulnerabile agli attacchi al canale laterale esecuzione speculativo cross-VM.
 
Mentre si lavora con i partner di settore ulteriori aggiornamenti possono diventare disponibili.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [protezione di Azure i clienti da vulnerabilità CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).