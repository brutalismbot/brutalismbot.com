require "rake/clean"

task :default => %i[terraform:plan]
CLOBBER.include ".terraform"
CLEAN.include ".terraform/terraform.zip"

namespace :terraform do
  directory ".terraform" do
    sh "terraform init"
  end

  desc "Run terraform init"
  task :init => %w[.terraform]

  ".terraform/terraform.zip".tap do |planfile|

    file planfile, order_only: %w[.terraform] do
      sh "terraform plan -out #{planfile}"
    end

    desc "Run terraform apply"
    task :apply => [planfile] do
      sh "terraform apply #{planfile}"
    end

    desc "Run terraform plan"
    task :plan => [planfile]
  end
end

namespace :aws do
  namespace :s3 do
    desc "List S3 contents"
    task :ls do
      sh "aws s3 ls s3://www.brutalismbot.com/"
    end

    desc "Sync local files with S3"
    task :sync do
      sh "aws s3 sync www s3://www.brutalismbot.com/"
    end
  end

  namespace :cloudfront do
    desc "Invalidate CloudFront cache"
    task :invalidate => %i[terraform:init] do
      sh <<~EOS
        aws cloudfront create-invalidation \
        --distribution-id $(terraform output cloudfront_distribution_id) \
        --paths '/*'
      EOS
    end
  end
end

desc "Start local HTTP server"
task :up do
  sh "ruby -run -e httpd www"
end