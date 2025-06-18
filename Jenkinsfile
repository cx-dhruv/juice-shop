pipeline {
    agent any


    environment {
        BASE_URI = 'https://ind.ast.checkmarx.net'
        CX_TENANT = 'sudha'
        OUTPUT_DIR = "${WORKSPACE}/output_fold"
        //GIT_REPO = 'https://github.com/cx-dhruv/juice-shop.git'
        _JAVA_OPTIONS = "-Xmx4096m"
    }

    options {
        timestamps()
    }

    stages {
        // stage('Checkout Code') {
        //     steps {
        //         git url: "${env.GIT_REPO}"
        //     }
        // }

        stage('Checkmarx AST Scan') {
            steps {
                script {
                    checkmarxASTScanner(
                        useOwnAdditionalOptions: true, 
                        additionalOptions: '--sast-incremental=true', 
                        serverUrl: 'https://ind.ast.checkmarx.net/', 
                        baseAuthUrl: 'https://ind.iam.checkmarx.net/', 
                        tenantName: "${env.CX_TENANT}", 
                        branchName: 'master', 
                        checkmarxInstallation: 'cxone-ind', 
                        projectName: 'cx-dhruv/juice-shop'
                    )
                }
            }
        }

        // stage('Checkmarx DAST Scan') {
        //     steps {
        //         echo 'Running Checkmarx DAST Scan...'
        
        //         script {
        //             def outputDir = "output_fold"
        
        //             bat "if not exist ${outputDir} mkdir ${outputDir}"
        
        //             def ret = bat(
        //                 script: """
        //                     docker run --rm ^
        //                         --user 0 ^
        //                         -v %WORKSPACE%:/config ^
        //                         -v %WORKSPACE%\\${outputDir}:/output ^
        //                         -e CX_APIKEY=${CX_APIKEY} ^
        //                         checkmarx/dast:latest ^
        //                         web ^
        //                         --base-url=${BASE_URI} ^
        //                         --environment-id=${ENVIRONMENT_ID} ^
        //                         --config=/config/juiceshop_automation_zap.yaml ^
        //                         --log-level=info ^
        //                         --output=/output
        //                 """.stripIndent(),
        //                 returnStatus: true
        //             )
        
        //             if (ret != 0) {
        //                 error("DAST scan failed with exit code ${ret}")
        //             }
        //         }
        //     }
        //}

    }
}
