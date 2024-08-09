from datetime import datetime, timedelta
from azure.storage.blob import BlobServiceClient, generate_blob_sas, BlobSasPermissions
import pandas as pd
import comtrade
import numpy as np
import requests

#enter credentials
account_name = 'dtftest1'
account_key = 'IA+HXO3RbfLRHebEvjkusvAUMhX88l9OSGK3fTCFd/gcS6Digzkw7q3TlOo3zTmAuOvdIaHYBUO++ASt6kfJ9Q=='
container_name = 'dtf'

#create a client to interact with blob storage
connect_str = 'DefaultEndpointsProtocol=https;AccountName=' + account_name + ';AccountKey=' + account_key + ';EndpointSuffix=core.windows.net'
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

#use the client to connect to the container
container_client = blob_service_client.get_container_client(container_name)

#get a list of all blob files in the container
blob_list = []
for blob_i in container_client.list_blobs():
    blob_list.append(blob_i.name)
    

df_list = []
#generate a shared access signiture for files and load them into Python
for blob_i in blob_list:
    #generate a shared access signature for each blob file
    sas_i = generate_blob_sas(account_name = account_name,
                                container_name = container_name,
                                blob_name = blob_i,
                                account_key=account_key,
                                permission=BlobSasPermissions(read=True),
                                expiry=datetime.utcnow() + timedelta(hours=1))
    
    sas_url = 'https://' + account_name+'.blob.core.windows.net/' + container_name + '/' + blob_i + '?' + sas_i
    
    df = pd.read_csv(sas_url, delimiter='\t')
    df_list.append(df)
    local_filename = f"{blob_i}"
    response = requests.get(sas_url)
    with open(local_filename, 'wb') as f:
        f.write(response.content)
    #print(f"File downloaded and saved as {local_filename}")

name = blob_i[:-3]
CFG = name + "CFG"
DAT = name + "DAT"
test = comtrade.load_as_dataframe(CFG, DAT, encoding="iso-8859-1")
print(test)
