
## Steps to generate OPENAPI /On_search parser
      *If the parser is already generated, simply install the Prerequisites and run ondc-protocol-for-api-logistics-client/ONDC_JSON_serializer.py and check the generated_json folder. this would be the serialized version of on_search_demo.json
      *If not, follow the steps to dereference the source yaml and generate the parser
Prerequisites 
```
   npm install -g @apidevtools/swagger-cli
   pip install pyyaml
   pip install jmespath
   pip install openapi-python-client
```
* logistics.yaml file must be dereferenced before it is being used for generating parser to produce a dereferenced logistics.yaml file, use this command
  ```
   swagger-cli bundle --outfile dereferenced_logistics.yaml --dereference --type yaml logistics.yaml
  ```
* then to generate the parser, use this command
  ```
   openapi-python-client generate --path dereferenced_logistics.yaml
  ```
* or to update a pre-existing parser, use this command
  ```
  openapi-python-client update --path dereferenced_logistics.yaml
  ```
* This will generate ondc-protocol-api-for-logistics-client folder with all the necessary models and API endpoints
* Then move these files to the ondc-buyer-client/ondc-protocol-api-for-logistics-client folder
  ```
  ONDC_callback_parser.py
  ONDC_JSON_serializer.py
  on_search_demo.json - (Right now, this will be the file that will get parsed)
  ```
* Create a new folder named generated_json in ondc-buyer-client/ondc_protocol-api-for-logistics-client
* Run the ONDC_JSON_serializer.py to parse on_search_demo.json file
* Check the generated_json folder, it will now contain an output.json file
