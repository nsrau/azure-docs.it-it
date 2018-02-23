> [!NOTE]
> È possibile usare DNS di Azure per configurare un nome DNS personalizzato per le app Web di Azure. Per altre informazioni, vedere [Usare il servizio DNS di Azure per specificare impostazioni di dominio personalizzate per un servizio di Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Accedere al sito Web del provider di dominio.

Individuare la pagina relativa alla gestione dei record DNS. Poiché ogni provider di dominio ha una propria interfaccia per i record DNS, vedere la documentazione del provider. Cercare le aree del sito denominate **Domain Name** (Nome di dominio), **DNS** o **Name Server Management** (Gestione server dei nomi). 

È spesso possibile visualizzare la pagina dei record DNS visualizzando le informazioni dell'account e cercando un collegamento come **My domains** (Domini personali). Passare alla pagina e quindi cercare un collegamento con un titolo simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

La schermata seguente è un esempio di pagina di record DNS:

![Pagina record DNS di esempio](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

Nello screenshot di esempio si seleziona **Aggiungi** per creare un record. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi. Anche in questo caso, vedere la documentazione del provider.

> [!NOTE]
> Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento separato di tipo **Salva modifiche**. 
