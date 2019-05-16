https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1604
#### Copying Public Key Manually
If you do not have password-based SSH access to your server available, you will have to complete the above process manually.

We will manually append the content of your id_rsa.pub file to the ~/.ssh/authorized_keys file on your remote machine.

To display the content of your id_rsa.pub key, type this into your local computer:
```
cat ~/.ssh/id_rsa.pub
```
You will see the key's content, which should look something like this:

Output
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCqql6MzstZYh1TmWWv11q5O3pISj2ZFl9HgH1JLknLLx44+tXfJ7mIrKNxOOwxIxvcBF8PXSYvobFYEZjGIVCEAjrUzLiIxbyCoxVyle7Q+bqgZ8SeeM8wzytsY+dVGcBxF6N4JS+zVk5eMcV385gG3Y6ON3EG112n6d+SMXY0OEBIcO6x+PnUSGHrSgpBgX7Ks1r7xqFa7heJLLt2wWwkARptX7udSq05paBhcpB0pHtA1Rfz3K2B+ZVIpSDfki9UVKzT8JUmwW6NNzSgxUfQHGwnW7kj4jp4AT0VZk3ADw497M2G/12N0PPB5CnhHf7ovgy6nL1ikrygTKRFmNZISvAcywB9GVqNAVE+ZHDSCuURNsAInVzgYo9xgJDW8wUw2o8U77+xiFxgI5QSZX3Iq7YLMgeksaO4rBJEa54k8m5wEiEE1nUhLuJ0X/vh2xPff6SQ1BL/zkOhvJCACK6Vb15mDOeCSq54Cr7kvS46itMosi/uS66+PujOO+xt/2FWYepz6ZlN70bRly57Q06J+ZJoc9FfBCbCyYH7U/ASsmY095ywPsBo1XQ9PqhnN1/YOorJ068foQDNVpm146mUpILVxmq41Cj55YKHEazXGsdBIbXWhcrRf4G2fJLRcGUr9q8/lERo9oxRm5JFX6TCmj6kmiFqv+Ow9gI0x8GvaQ== demo@test
```
Access your remote host using whichever method you have available.

Once you have access to your account on the remote server, you should make sure the ~/.ssh directory exists. This command will create the directory if necessary, or do nothing if it already exists:

mkdir -p ~/.ssh
Now, you can create or modify the authorized_keys file within this directory. You can add the contents of your id_rsa.pub file to the end of the authorized_keys file, creating it if necessary, using this command:

echo public_key_string >> ~/.ssh/authorized_keys
In the above command, substitute the public_key_string with the output from the cat ~/.ssh/id_rsa.pub command that you executed on your local system. It should start with ssh-rsa AAAA....

Finally, we’ll ensure that the ~/.ssh directory and authorized_keys file have the appropriate permissions set:

chmod -R go= ~/.ssh
This recursively removes all “group” and “other” permissions for the ~/.ssh/ directory.

If you’re using the root account to set up keys for a user account, it’s also important that the ~/.ssh directory belongs to the user and not to root:

chown -R sammy:sammy ~/.ssh
In this tutorial our user is named sammy but you should substitute the appropriate username into the above command.

We can now attempt passwordless authentication with our Ubuntu server.

