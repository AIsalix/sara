name: Publish 🚀

on:
  workflow_dispatch:
  push:
    branches:
    - main

env:
  AZURE_WEBAPP_NAME: current-time-service
  AZURE_WEBAPP_PACKAGE_PATH: "./publish"

jobs:
  publish:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: '6.0.x'
          
      - name: Restore
        run: dotnet restore ./CurrentTimeService.sln
        
      - name: Build
        run: dotnet build ./CurrentTimeService.sln --configuration Release --no-restore
        
      - name: Publish
        run: dotnet publish ./CurrentTimeService.sln --configuration Release --no-build --output '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}'
        
      - name: Deployment
        uses: azure/webapps-deploy@v2
        with:
          app-name: ${{ env.AZURE_WEBAPP_NAME }}
          publish-profile: ${{ APP_AZURE1 }}
          package: "${{ env.AZURE_WEBAPP_PACKAGE_PATH }}"
