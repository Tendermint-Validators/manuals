# Migrate Validator
This manual describes the process required for migrating an existing Tendermint Validator<br>
from one host to another.<br>

## Migrate by Mnemonic
The prefered method is to migrate by mnemonic. This method does not require any files from<br>
the original validator and thus does not import any garbage from the previous installation.

## Migrate by File
Migrate by file is possible when you dont have access to the mnemonic. This method requires<br>
you to migrate the configuration from your old Validator to your new Validator. Any garbage<br>
in the configuration from the old Validator will be copied as well.
