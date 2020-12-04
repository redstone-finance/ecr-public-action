# ecr-public-action
`ecr-public-action` is a Github Action that allows you to docker `build`, `tag` and `publish` to **Amazon ECR Public** in the Github Action workflow.


# sample

```yaml
- name: build and push
  id: build-and-push
  uses: pahud/ecr-public-action@12e969f
  env:
    AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
    AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  with:
    context: .devcontainer
    dockerfile: ./.devcontainer/Dockerfile
    tags: |
      public.ecr.aws/d7p2r8s3/${{ steps.repoName.outputs.reponame }}:latest
      public.ecr.aws/d7p2r8s3/${{ steps.repoName.outputs.reponame }}:${{ steps.sha.outputs.sha8 }}
```

# Full Sample

```yaml
ecr_public:
  name: ECR Public
  runs-on: ubuntu-latest
  steps:
    - name: Get repo name
      id: repoName
      run: echo "::set-output name=reponame::$(echo ${{github.repository}} | cut -d '/' -f 2)"
    - name: Get short SHA
      id: sha
      run: echo "::set-output name=sha8::$(echo ${GITHUB_SHA} | cut -c1-8)"
    - name: Checkout
      uses: actions/checkout@v2
    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1
    - name: Build and Push to ECR public
      id: build-and-push
      uses: pahud/ecr-public-action@12e969f
      with:
        context: .devcontainer
        dockerfile: ./.devcontainer/Dockerfile
        tags: |
          public.ecr.aws/d7p2r8s3/${{ steps.repoName.outputs.reponame }}:latest
          public.ecr.aws/d7p2r8s3/${{ steps.repoName.outputs.reponame }}:${{ steps.sha.outputs.sha8 }}
```