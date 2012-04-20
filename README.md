FTP Bulk Transfer
=============

Basically supports just asynchronous uploads. Downloads in future.

How to use
----------

### Uploads

Use this to asynchronously upload a bulk set off files. 

    $ftp_bulk = new FTPBulkTransfer($ftp_server, $ftp_port, $ftp_username, $ftp_pass, $maximum_ftp_connections);
    if($ftp_bulk->is_open()) {
        // add all the files to be uploaded
        $ftp_bulk->put(
            $remotefilename1, 
            $localfilename1
        );
        
        $ftp_bulk->put(
            $remotefilename2, 
            $localfilename2
        );
        
        // perform any uploads
        while($ret = $ftp_bulk->poll()) 
            ;   // do other stuff while waiting
        
    } else {
        // fallback upload - i.e just use php's ftp functions
    }
    $ftp_bulk->close();

### Callbacks

Success

    function upload_success($transfer, $ftp_bulk) {
        global $file_count; // amount of files that were added
        if($file_count - $ftp_bulk->transfer_count())
            $percent = (int) (($file_count - $ftp_bulk->transfer_count()) / $file_count * 100);
        else 
            $percent = "0";
        echo "<strong>[$percent%]</strong> Uploaded file: ".$transfer['remote'];
    }
    
    global $file_count;
    
    // foreach file
    
    $ftp_bulk->put(
        $remotefilename, 
        $localfilename,
        '', // error callback
        'upload_success'
    );
    ++$file_count;
    
    // endforeach
    
    

Failure

    function upload_error($transfer, $ftp_bulk) {
        echo Failed to upload file: ".$transfer['remote'];
    }
    
    // foreach file
    
    $ftp_bulk->put(
        $remotefilename, 
        $localfilename,
        'upload_error', // error callback
        '' // success callback
    );
    
    // endforeach
