# Введение 
Создает базовый образ для ускорения сборки/анализа проектов .netCore сервисом SonarQube.

# Описание проекта
Образ построен на базе ubuntu образа от майкрософт: mcr.microsoft.com/dotnet/core/sdk:3.1-bionic
1.	Дополнительно установлены:
    * openJDK 8
    * SonarScanner

# Использование
Пример использования показан в Dockerfile.use
*    Для проверки PullRequest
``` bash
RUN dotnet sonarscanner begin \
	/k:$SONAR_PROJECT_KEY \
	/d:sonar.host.url=$SONAR_HOST \
	/d:sonar.login=$SONAR_TOKEN  \
	/d:sonar.pullrequest.key=$PULLREQUEST_KEY  \
	/d:sonar.pullrequest.branch=$PULLREQUEST_BRANCH  \
	/d:sonar.pullrequest.base=$PULLREQUEST_BASE  \
	/d:sonar.pullrequest.provider=$PULLREQUEST_PROVIDER  \
	/d:sonar.pullrequest.vsts.instanceUrl=$PULLREQUEST_INSTANCE_URL  \
	/d:sonar.pullrequest.vsts.project=$PULLREQUEST_PROJECT \
	/d:sonar.pullrequest.vsts.repository=$PULLREQUEST_REPO \
	/d:sonar.coverageReportPaths=$COVERAGE \
	/d:sonar.cs.vstest.reportsPaths=$TESTRESULTS \
	/v:$VERSION
```
*    Для Проверки проекта
``` bash
RUN dotnet sonarscanner begin\
	/k:$SONAR_PROJECT_KEY \
    /d:sonar.verbose=true \
	/d:sonar.host.url=$SONAR_HOST \
	/d:sonar.login=$SONAR_TOKEN  \
	/d:sonar.branch.name=$SOURCE_BRANCH \
	/d:sonar.coverageReportPaths=$COVERAGE \
	/d:sonar.cs.vstest.reportsPaths=$TESTRESULTS \
	/v:$VERSION 
```
``` bash
RUN dotnet build Project.sln  
RUN dotnet test  
RUN dotnet sonarscanner end /d:sonar.login=$SONAR_TOKEN
```

Смотри также:
- [SonarQube](https://www.sonarqube.org/)
