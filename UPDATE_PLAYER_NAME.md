# How to change a BrightSign unit's name 

### Using the Local Development Web Server (LDWS) API

We're going to use the Local Development Web Server (LDWS, sometimes called the DWS) API to change the name of a BrightSign unit. This applies to units using the Local File Networking (LFN) feature.

This also requires that the LDWS is running on the unit and that you have access to it.

Finally, you'll need a program that can make API calls with Digest Authentication. We're going to use Postman - the free version is sufficient for this task.

## 1. Set the LDWS password

- In a browser, navigate to `http://<unit-ip-address>`. Replace `<unit-ip-address>` with the actual IP address of your BrightSign unit.
- Go to the `Control` tab and set (or change, or delete) the DWS password using the `Set Password` option.

No password is required to change the unit name, but if one is set, you'll need to know it.

## 2. Load up Postman and read the registry data

> [!NOTE]
> Reading the registry data is not required to rename a unit, but it will help you confirm the current unit name and ensure that the LDWS API is working correctly.

- Open Postman and create a new request
- Set the request type to `GET`
- Enter the URL: `http://<unit-ip-address>/api/registry`
- If you set a password in the previous step, go to the `Authorization` tab,
    - Select type `Digest Auth`, and enter the username `admin` and the password you set in step 1
- Click `Send`

You should see a response with the registry data in JSON format, similar to:

```json
{
    "data": {
        "result": {
            "success": true,
            "value": {
                "autorun": {
                    "lasttried": ""
                },
                "brightscript": {
                    "createdby": "Supervisor 2.0.23"
                },
                "networking": {
                    "bbhf": "undefined",
                    
                    "other_data_here_we_don't_care_about": "other values",

                    "ul": "",
                    "un": "BS23-CCTV",
                    "unm": "unitNameOnly",
                    "up": "no",
                    "ut": " 0",
                    "version": "3.0.0",
                    "vle": "no"
                }
            }
        }
    }
}
```

Here we can see the current unit name in the `un` field under `networking` - in this case, `BS23-CCTV`.

## 3. Change the unit name
- Duplicate the request you just created in Postman
- Change the request type to `PUT`
- Enter the URL: `http://<unit-ip-address>/api/registry/networking/un`
- Go to the `Body` tab, select `raw`, and set the type to `JSON`
- Enter the new unit name in the body, like this:

    ```json
    {
        "value": "NewUnitName"
    }
    ```
- Click `Send` to change the unit name

## 4. Flush the registry cache
- Duplicate the request again
- Set the request type to `PUT`
- Enter the URL: `http://<unit-ip-address>/api/registry/flush`
- Click `Send` to flush the registry cache to persistent storage


## 5. Reload the LDWS web interface

In your browser, navigate to `http://<unit-ip-address>` again. You should see the new unit name displayed in the web interface.

It's probably a good idea to reboot the unit at this point to ensure that the new name is fully applied across all services.