# Google drive upload

Google drive upload is a Bash script based on v3 google APIs to upload files/directories into google drive. This is a minimalistic shell script which utilizes google OAuth2.0 device flow to generate access tokens to authorize application to upload files to your google drive.

Further usage documentation can be found at my blog page [Labbots.com](https://labbots.com/google-drive-upload-bash-script/ "Labbots.com").

## Dependencies

This script does not have very many dependencies. Most of the dependencies are available by default in most linux platforms. This script explicitly requires the following packages:

- Bash ( 4.x + )
- Curl
- grep
- sed ( Stream editor )
- file/mimetype ( generating mimetype for extensionless files )
- find command ( for recursive uploading )
- xargs ( for parallel uploading )

## Features

- Upload files and folders normally/parallelly.
- Upload sub-folders and content inside it hierarchically.
- Upload multiple files / folders with single command.
- Update (overwrite) your files/folder, like synchronisation.
- Config file support ( easy to use script on multiple machines ).
- Uses latest gdrive v3 api.
- Share files after uploading ( to an email or just anyone ).
- Pretty Logging.

## Usage

When the script is executed for the first time. It asks for few configuration variables interactively to connect with google APIs. The script requires Client ID and Client secret to access the APIs which can be generated at [google console].

### Generating Google OAuth2 Credentials

- Log into google developer console at [google console].
- Create new Project or use existing project if you don't have a project already created.
- Create new OAuth 2.0 Credentials
  - Select Application type "other"
  - Provide name for the new credentials
  - This would provide a new client ID and Client Secret
- Enable Google Drive API for the project under "Library"

*Note:* When the script is run for the first time. The script must be authorized in browser. This may show a certificate warning if the application authorized domain is not configured on google console project.

### Running the script

Script also asks for root folder to be set to which the script uploads documents by default.
The default folder will be the root of your google drive. If you want to upload documents to any specific directory by default then provide the google folder ID or URL of that folder when root folder ID/URL is requested.

For example
`https://drive.google.com/drive/folders/8ZzWiO_pMAtBrLpARBZJNV09TVmM`

In the above URL. The folder id is ***8ZzWiO_pMAtBrLpARBZJNV09TVmM***

Note: You can input either URL or ID.

The default configurations of the script are store under **$HOME/.googledrive.conf**

The script can be used in the following way

    ./upload.sh <filepath/folderpath> <foldername>
Above command will create a folder under the pre-configured root directory and upload the specified file under the folder name. If the folder already exists then the file is uploaded under the folder.

Other Options available are

    -C | --create-dir <foldername> - option to create directory. Will provide folder id. Can be used to specify workspace folder for uploading files/folders.

    -r | --root-dir <google_folderid> - google folder id or url to which the file/directory to upload.
    
    -s | --skip-subdirs - Skip creation of sub folders and upload all files inside the INPUT folder/sub-folders in the INPUT folder, use this along with -p/--parallel option to speed up the uploads.
    
    -p | --parallel <no_of_files_to_parallely_upload> - Upload multiple files in parallel, Max value = 10, use with folders.
        
        Note :
          This command is only helpful if you are uploading many files which aren't big enough to utilise your full bandwidth, using it otherwise will not speed up your upload and even error sometimes,
          1 - 6 value is recommended, but can use upto 10. If errors with a high value, use smaller number.
          Beaware, this isn't magic, obviously it comes at a cost of increased cpu/ram utilisation as it forks multiple bash processes to upload ( google how xargs works with -P option ).              
    
    -o | --overwrite - Overwrite the files with the same name, if present in the root folder/input folder, also works with recursive folders and single/multiple files.
    
    -f | --[file/folder] - Specify files and folders explicitly in one command, use multiple times for multiple folder/files.   

        For uploading multiple input into the same folder:
           1. Use -C / --create-dir ( e.g `./upload.sh -f file1 -f folder1 -f file2 -C <folder_wherw_to_upload>` ) option.
           2. Give two initial arguments which will use the second argument as the folder you wanna upload ( e.g: `./upload.sh filename <folder_where_to_upload> -f filename -f foldername` ).
           
        This flag can also be used for uploading files/folders which have `-` character in their name, normally it won't work, because of the flags, but using `-f -[file|folder]namewithhyphen` works. Applies for -C/--create-dir too.
        Also, as specified by longflags ( `--[file|folder]` ), you can simultaneously upload a folder and a file.
        Incase of multiple -f flag having duplicate arguments, it takes the last duplicate of the argument to upload, in the same order provided.
                            
    -S | --share <optional_email_address>- Share the uploaded input file/folder, grant reader permission to provided email address or to everyone with the shareable link.
    
    -q | --quiet - Supress the normal output, only show success/error upload messages for files, and one extra line at the beginning for folder showing no. of files and sub folders.
    
    -v | --verbose - Display detailed message (only for non-parallel uploads).
    
    -V | --verbose-progress - Display detailed message and detailed upload progress(only for non-parallel uploads).
    
    -i | --save-info <file_to_save_info> - Save uploaded files info to the given filename."
    
    -h | --help - Display usage instructions.
    
    -z | --config - Override default config file with custom config file.
    
    -D | --debug - Display script command trace."

To create a folder:

    ./upload.sh -C <foldername> -r <optional-root-dir-id> 

This will give the folder id of the newly created folder which can be used to upload files to specific directory in google drive.

To Upload file to specific google folder

    ./upload.sh -r <google-folder-id> <file/directory-path>

The script also allows to upload directories. If directory path is provided as argument to the script then the script recursively uploads all the sub-folder and files present in the heirarchial way as it is present on the local machine.

## Inspired By

- [github-bashutils] - soulseekah/bash-utils
- [deanet-gist] - Uploading File into Google Drive

## License

MIT

[github-bashutils]: <https://github.com/soulseekah/bash-utils>
[deanet-gist]:<https://gist.github.com/deanet/3427090>
[google console]:<https://console.developers.google.com>
