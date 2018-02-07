| **Componente** | **Requisito** |
| --- |---|
| Core CPU| 8 |
| RAM | 12 GB|
| Numero di dischi | 3, inclusi disco del sistema operativo, disco della cache del server di elaborazione e unità di conservazione per il failback |
| Spazio libero su disco (cache del server di elaborazione) | 600 GB
| Spazio libero su disco (disco di conservazione) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Impostazioni locali del sistema operativo | Inglese (en-us)|
| Versione di VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Criteri di gruppo| Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Altre informazioni](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | - Nessun sito Web predefinito preesistente <br> - Abilitare l'[autenticazione anonima](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Abilitare l'impostazione di [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443<br>|
| Tipo di scheda di interfaccia di rete | VMXNET3 (quando distribuito come macchina virtuale VMware) |
| Tipo di indirizzo IP | statico |
| Accesso a Internet | Il server deve poter accedere a questi URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (non necessario per i server di elaborazione con scalabilità orizzontale) <br> - time.nist.gov <br> - time.windows.com |
| Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)|
