<!--routify:options title="Form Akun Perkiraan"-->
<script lang="ts">
  import type { FormStore } from "@deboxsoft/svelte-forms";

  import { onMount } from "svelte";
  import { url, params } from "@roxi/routify";
  import Icon from "@deboxsoft/svelte-theme-limitless/components/Icon.svelte";
  import SaveIcon from "@deboxsoft/svelte-icons/SaveTwoTone.svelte";
  import Form from "@deboxsoft/svelte-forms/Form.svelte";
  import InputField from "@deboxsoft/svelte-forms/InputField.svelte";
  import PageLayout from "__@root/layout/PageLayout.svelte";
  import AutoComplete from "__@comps/AutoComplete.svelte";
  import { getAccountContext } from "__@modules/transaksi";
  import { get, writable } from "svelte/store";

  const {
    accountStore,
    createAccount,
    updateAccount,
    accountTypeList,
    getAccountType,
    getAccount
  } = getAccountContext();
  let formStore: FormStore;
  let formEl: HTMLFormElement;
  let account;
  onMount(() => {
    formStore.addField("type");
  });

  function changeAccountValueHandler(value) {
    console.log("change");
    if (formStore) {
      console.log("change value", value);
      formStore.setValue("type", value.id);
      if (formStore.isTouched("type")) {
        formStore.validateField("type");
      }
    }
  }

  function submitHandler(e) {
    formStore.submit(async (values: any) => {
      if (account) {
        console.log("update", values);
        await updateAccount($account.id, values);
      } else {
        console.log("insert", values);
        await createAccount(values);
      }
    });
  }
  $: {
    if ($params.id) {
      account = getAccount($params.id);
      console.log(formStore && $account?.type, "paramns id");
      formStore && (formStore.setValue("type", $account?.type));
      console.log(get(formStore?.getValue("type")));
    }
  }
</script>

<PageLayout breadcrumb={{ title: 'Form Akun perkiraan' }}>
  <div class="header-elements" slot="header-elements">
    <a
      href={$url('./')}
      class="btn btn-link btn-float text-default"
      target="_self"
      on:click|preventDefault={submitHandler}>
      <Icon class="text-primary" size="large" component={SaveIcon} />
    </a>
  </div>
  <Form bind:formStore bind:ref={formEl}>
    <div class="d-flex flex-column h-100">
      <div class="card">
        <div class="card-body">
          <div class="form-group">
            <label for="code">Kode</label>
            <InputField
              id="code"
              name="code"
              type="text"
              class="form-control"
              placeholder="Kode"
              value={$account && $account.code} />
          </div>
          <div class="form-group">
            <label for="name">Nama</label>
            <InputField
              id="name"
              name="name"
              type="text"
              class="form-control"
              placeholder="Nama"
              value={$account && $account.name} />
          </div>
          <div class="form-group">
            <div>Jenis Akun</div>
            <AutoComplete
              selectedItem={$account && getAccountType($account.type)}
              onChange={changeAccountValueHandler}
              inputClassName="form-control"
              placeholder="Jenis Akun"
              items={accountTypeList()}
              labelFunction={(accountType) => (accountType ? `${accountType.code} ${accountType.label}` : null)}
              keywordsFunction={(accountType) => (accountType ? `${accountType.code} ${accountType.label}` : null)} />
          </div>
        </div>
      </div>
    </div>
  </Form>
</PageLayout>
