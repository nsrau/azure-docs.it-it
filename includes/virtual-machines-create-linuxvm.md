
1. Accedere alla sottoscrizione di Azure seguendo i passaggi elencati in [Connettersi ad Azure dall'interfaccia della riga di comando di Azure](../articles/xplat-cli-connect.md).
2. Assicurarsi che sia attiva la modalità di distribuzione classica usando:
   
        azure config mode asm
3. Individuare l'immagine di Linux che si desidera caricare dalle immagini disponibili:
   
        azure vm image list | grep "Linux"
   
   In una finestra del prompt dei comandi di Windows usare **find** anziché grep.
4. Usare `azure vm create` per creare una nuova macchina virtuale con l'immagine di Linux dall'elenco precedente. Questo passaggio crea un nuovo servizio cloud, nonché un nuovo account di archiviazione. È inoltre possibile connettere questa macchina virtuale a un servizio cloud esistente con un’opzione `-c`. Viene inoltre creato un endpoint SSH per l'accesso alla macchina virtuale Linux con l'opzione `-e`.
   
        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB -g adminUser -p P@ssw0rd! -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating VM
        info:    vm create command OK
   
   > [!NOTE]
   > Per una macchina virtuale Linux, è necessario fornire l'opzione `-e` in `vm create`. Non è possibile abilitare SSH dopo la creazione della macchina virtuale. Per altre informazioni su SSH, vedere la pagina relativa all'[Uso di SSH con Linux in Azure](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).
   > 
   > 
   
    L'immagine *b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB* è quella selezionata dall'elenco delle immagini nel passaggio precedente. *MyTestVM* è il nome della nuova macchina virtuale, mentre *adminUser* è il nome utente usato per SSH nella macchina virtuale. È possibile sostituire queste variabili in base alle proprie esigenze. Per altre informazioni su questo comando, vedere [Uso dell'interfaccia della riga di comando di Azure con il modello di distribuzione classica](../articles/virtual-machines-command-line-tools.md).
5. La macchina virtuale Linux appena creata viene visualizzata nell'elenco specificato da:
   
        azure vm list
6. È possibile verificare gli attributi della macchina virtuale utilizzando il comando:
   
        azure vm show MyTestVM
7. La macchina virtuale appena creata è pronta per iniziare con il comando `azure vm start`.

## Passaggi successivi
Per informazioni su tutti questi comandi della macchina virtuale dell'interfaccia della riga di comando di Azure, leggere [Uso dell'interfaccia della riga di comando di Azure con l'API di distribuzione classica](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0824_2016-->