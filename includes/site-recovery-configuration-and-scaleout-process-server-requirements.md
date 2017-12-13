| **Hardware** | |
| --- |---|
| Numero di core CPU| 8 |
| RAM| 12 GB|
| Numero di dischi | 3 <br><br> - Disco del sistema operativo<br> - Disco della cache del server di elaborazione<br> - Unità di conservazione (per il failback)|
| Spazio libero su disco (cache del server di elaborazione) | 600 GB
| Spazio libero su disco (disco di conservazione) | 600 GB|
| **Software** | |
| Versione del sistema operativo | Windows Server 2012 R2 <br> Windows Server 2016 |
| Impostazioni locali del sistema operativo | Inglese (en-us)|
| Versione di VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Ruoli di Windows Server | Non abilitare i ruoli seguenti: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Criteri di gruppo| I criteri di gruppo seguenti non devono essere abilitati nel server <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> **Nota:** per altre informazioni su questi criteri di gruppo, vedere [qui](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| Configurazione di Internet Information Services (IIS) | - Nessun sito Web predefinito preesistente <br> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> - Nessun sito Web o applicazione preesistente deve essere in ascolto sulla porta 443<br>|
| **Rete** | |
| Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware) |
| Tipo di indirizzo IP | Static |
| Accesso a Internet | Il server deve poter accedere all'URL seguente direttamente o tramite un server proxy: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (non necessario per i server di elaborazione con scalabilità orizzontale) <br> - time.nist.gov <br> - time.windows.com |
| Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)|
