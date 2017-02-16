* In **Aggiungi vCenter** specificare un nome descrittivo per il server vCenter o l'host vSphere e specificare l'indirizzo IP o il nome di dominio completo del server. Lasciare la porta 443, a meno che i server VMware siano configurati per l'ascolto delle richieste su una porta diversa. Selezionare quindi l'account che si deve connettere al server di VMware vCenter o vSphere ESXi. Fare clic su **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Se si aggiunge il server VMwaer vCenter o l'host VMware vSphere con un account che non ha privilegi di amministratore per il server vCenter o il server host, assicurarsi che questi privilegi siano abilitati per gli account: Datacenter, Datastore, Folder, Host, Network, Resource, Virtual machine, vSphere Distributed Switch. Il server VMware vCenter deve avere anche il privilegio Storage views.


<!--HONumber=Jan17_HO3-->


