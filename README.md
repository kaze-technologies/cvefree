# cvefree
Kaze's openly available CVE vulnerability data.

# Getting the data

In the comprehensive form we publish the vulnerability data, it is too large to be held and updated on github*. Therefore, you will need to download the full file from our servers. You can use the following curl command to download the file:

```
curl https://kazepublic.blob.core.windows.net/cvefree/data.json --output data.json
```

*We are open to the idea of breaking down the CVE data per year or month into the repository itself if there is demand for it

# Visualising the data

Navigate the notebooks in ```notebooks/``` and open and run them for basic visulisations. You can begin doing any processing or visualisation of this data in any prefered language by parsing the downloaded ```data.json``` file.