> Under dev


## Notes

- The region you create your notebook instance will be insulated from other regions. In other words, you can only access (and find) the notebook instance from that region.
- To be able to open the notebook locally on VSCode, you need to setup a vscode tunnel in the AWS Notebook instance EACH TIME you start the notebook.
- In addition, to be able to pull / push from Github, you need to config an ssh key EACH TIME we start the notebook instance.

A better way to deal with this is by creating a Lifecycle script that automatically run every time the notebook instance starts.
