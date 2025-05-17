pipeline {
    agent any


    environment {
        CX_APIKEY = 'eyJhbGciOiJIUzUxMiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI5ZDMzNDA4Yy0yMGVjLTQ2ZjgtYWY5ZC1kZTljYmU0MWZlOTIifQ.eyJpYXQiOjE3NDI3MjQyNDEsImp0aSI6IjA2MGZlODE5LTBlN2YtNGNjNS1hOTQxLTM2NjRiMTYzYTQ1MyIsImlzcyI6Imh0dHBzOi8vZXUuaWFtLmNoZWNrbWFyeC5uZXQvYXV0aC9yZWFsbXMvYXN0X2FiZHVsX2Fuc2FyaSIsImF1ZCI6Imh0dHBzOi8vZXUuaWFtLmNoZWNrbWFyeC5uZXQvYXV0aC9yZWFsbXMvYXN0X2FiZHVsX2Fuc2FyaSIsInN1YiI6ImFmNjRhYjUzLWE0NjctNDZhZi1iMDBmLTVjNDI3NmE2YTNjYSIsInR5cCI6Ik9mZmxpbmUiLCJhenAiOiJhc3QtYXBwIiwic2lkIjoiODcwMjU4OWYtNTk0My00MmE4LWI2MjYtMGE3ZWEzNDJmODI5Iiwic2NvcGUiOiJyb2xlcyBwcm9maWxlIGdyb3VwcyBlbWFpbCBpYW0tYXBpIGFzdC1hcGkgb2ZmbGluZV9hY2Nlc3MifQ.aFQgNYbt3lgUbI6kKqIxEzyJWeuXx5zZNqxsYpwaMpaohE9Lk2NEAnVuhqYQfntJZU_FDugHgsRtGhymQ_IRKw'
        BASE_URI = 'https://eu.ast.checkmarx.net'
        CX_TENANT = 'ast_abdul_ansari'
        ENVIRONMENT_ID = '2ee7bc42-a210-4300-9f33-9749781b2feb'
        OUTPUT_DIR = "${WORKSPACE}/output_fold"
        GIT_REPO = 'https://github.com/cx-dhruv-s-pathak/juice-shop.git'
        _JAVA_OPTIONS = "-Xmx4096m"
    }

    options {
        timestamps()
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: "${env.GIT_REPO}"
            }
        }

        // stage('Checkmarx AST Scan') {
        //     steps {
        //         script {
        //             checkmarxASTScanner(
        //                 useOwnAdditionalOptions: true, 
        //                 additionalOptions: '--sast-incremental=true', 
        //                 serverUrl: 'https://ind.ast.checkmarx.net/', 
        //                 baseAuthUrl: 'https://ind.iam.checkmarx.net/', 
        //                 tenantName: "${env.CX_TENANT}", 
        //                 branchName: 'master', 
        //                 checkmarxInstallation: 'CxAST CLI', 
        //                 projectName: 'cx-dhruv-s-pathak/juice-shop'
        //             )
        //         }
        //     }
        // }

        stage('Checkmarx DAST Scan') {
            steps {
                echo 'Running Checkmarx DAST Scan...'
        
                script {
                    def outputDir = "output_fold"
        
                    bat "if not exist ${outputDir} mkdir ${outputDir}"
        
                    def ret = bat(
                        script: """
                            docker run --rm ^
                                --user 0 ^
                                -v %WORKSPACE%:/config ^
                                -v %WORKSPACE%\\${outputDir}:/output ^
                                -e CX_APIKEY=${CX_APIKEY} ^
                                checkmarx/dast:latest ^
                                web ^
                                --base-url=${BASE_URI} ^
                                --environment-id=${ENVIRONMENT_ID} ^
                                --config=/config/juiceshop_automation_zap.yaml ^
                                --log-level=info ^
                                --output=/output
                        """.stripIndent(),
                        returnStatus: true
                    )
        
                    if (ret != 0) {
                        error("DAST scan failed with exit code ${ret}")
                    }
                }
            }
        }

    }
}
