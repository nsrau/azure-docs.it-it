
1. Accedere alla sottoscrizione di Azure seguendo i passaggi elencati in[Connettersi ad Azure da CLI Azure](../articles/xplat-cli-connect.md).

2. Assicurarsi che sia attiva la modalità Gestione dei servizi utilizzando:

        azure config mode asm

3. Individuare l'immagine di Linux che si desidera caricare dalle immagini disponibili:

        azure vm image list | grep "Linux"

   In una finestra del prompt dei comandi di Windows usare **find** anziché grep.

4. Utilizzare`azure vm create`per creare una nuova macchina virtuale con l'immagine di Linux dall'elenco precedente. Questo passaggio crea un nuovo servizio cloud, nonché un nuovo account di archiviazione. È inoltre possibile connettere questa macchina virtuale a un servizio cloud esistente con un’opzione `-c`. Viene inoltre creato un endpoint SSH per l'accesso alla macchina virtuale Linux con l’opzione`-e`.

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-it-IT-30GB
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] Per una macchina virtuale Linux, è necessario fornire l’opzione`-e`in `vm create`; non è possibile abilitare SSH dopo aver creato la macchina virtuale. Per altre informazioni su SSH, vedere la pagina relativa all'[Uso di SSH con Linux in Azure](virtual-machines-linux-ssh-from-linux.md).

    Si noti che l'immagine *b39f27a8b8c64d52b05eac6a62ebad85\_\_Ubuntu-14\_04\_4-LTS-amd64-server-20160516-it-IT-30GB* è quella selezionata dall'elenco delle immagini nel passaggio precedente. *MyTestVM*è il nome della nostra nuova macchina virtuale, e*adminUser*è il nome utente che verrà utilizzato per SSH nella macchina virtuale. È possibile sostituire queste variabili in base alle proprie esigenze. Per altre informazioni su questo comando, vedere l'argomento relativo all'[uso dell'interfaccia della riga di comando di Azure con Gestione servizi di Azure](virtual-machines-command-line-tools.md).

5. La macchina virtuale Linux appena creata verrà visualizzata nell'elenco specificato da:

        azure vm list

6. È possibile verificare gli attributi della macchina virtuale utilizzando il comando:

        azure vm show MyTestVM

7. La macchina virtuale appena creata è pronta per iniziare con il comando `azure vm start`.

## Passaggi successivi
Per informazioni dettagliate su tutti i comandi della macchina virtuale dell'interfaccia della riga di comando di Azure, leggere l'argomento relativo all'[uso dell'interfaccia della riga di comando di Azure con l'API di Gestione dei servizi](../articles/virtual-machines-command-line-tools.md).

<!---HONumber=AcomDC_0608_2016-->