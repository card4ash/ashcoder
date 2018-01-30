How To Create a Sudo User on Ubuntu 
=======================================

.. code-block :: shell

       adduser username

Set and confirm the new user's password at the prompt. A strong password is highly recommended!

.. code-block :: shell

        Set password prompts:
        Enter new UNIX password:
        Retype new UNIX password:
        passwd: password updated successfully

Follow the prompts to set the new user's information. It is fine to accept the defaults to leave all of this information blank.

.. code-block :: shell

        User information prompts:
        Changing the user information for username
        Enter the new value, or press ENTER for the default
            Full Name []:
            Room Number []:
            Work Phone []:
            Home Phone []:
            Other []:
        Is the information correct? [Y/n]

Use the usermod command to add the user to the sudo group.

.. code-block :: shell

        usermod -aG sudo username

By default, on Ubuntu, members of the sudo group have sudo privileges.
Use the su command to switch to the new user account.

.. code-block :: shell

        su - username

.. code-block :: shell

        sudo ls -la /root