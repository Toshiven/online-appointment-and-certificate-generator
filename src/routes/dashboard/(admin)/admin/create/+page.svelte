<script>
    import toast, { Toaster } from 'svelte-french-toast'
    import Radio from '$components/Radio.svelte'

    let account_type
    let email
    let info = {}
    const options = [
        {
            value: 'user',
            label: 'User',
        },
        {
            value: 'employee',
            label: 'Employee',
        },
    ]
    let password = ''
    let confirm_password = ''
    let passwordError = ''

    const handle_submit = async (e) => {
        e.preventDefault()
        validatePassword()
        if (passwordError) {
            toast.error('Password does not meet the required conditions.')
            return
        }
        console.log(`Trying to create ${account_type} account`)
        const endpoint = `https://appt-cert-gen-api.itsdarkhere4ever.repl.co/api/${account_type}/register`
        const opts = {
            method: 'POST',
            headers: {
                'content-type': 'application/json',
                Authorization: `Bearer ${localStorage.getItem('access_token')}`,
            },
            body: JSON.stringify({
                email: email,
                password: password,
                info: info,
            }),
        }
        const resp = await fetch(endpoint, opts).then((res) => res.json())
        // NOTE: this shoule be displayed in the use
        console.log(`REGISTER ENDPOINT RESPONSE: ${JSON.stringify(resp)}`)
    }

    function handlePasswordInput(event) {
        password = event.target.value
        validatePassword()
    }

    // NOTE: this validation does not check if the password contains atleast one symbol!
    // also this does not check is the password contains atleast one uppercase character
    // this validation kinda works but i guess password-validator would make it easier xD
    function validatePassword() {
        const minLength = 8
        const maxLength = 100
        const digitRegex = /\d/

        if (password !== confirm_password) {
            passwordError = 'Password and confirm password does not match'
        } else if (password.length < minLength || password.length > maxLength) {
            passwordError =
                'Password must be minimum 8 and maximum 100 characters.'
        } else if (!digitRegex.test(password)) {
            passwordError = 'Password must contain at least one digit.'
        } else {
            passwordError = ''
        }
    }
</script>

<Toaster />

<div
    class="grid place-items-center mt-10 py-[60px] px-12 bg-white border border-gray-200 rounded-lg shadow"
>
    <div class="">
        <div>
            <!-- NOTE: PLEASE CONFIGURE THE STYLE FOR THIS  -->
            <Radio
                {options}
                fontSize={16}
                legend="Account Type"
                bind:userSelected={account_type}
            />
        </div>
        <form class="grid grid-cols-2 gap-6" on:submit={handle_submit}>
            <div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="first_name"
                        bind:value={info.first_name}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="First Name*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="last_name"
                        bind:value={info.last_name}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Last Name*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <select
                        bind:value={info.gender}
                        id="genders"
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                        required
                    >
                        <option value="" selected>Select gender...</option>
                        <option value="male">Male</option>
                        <option value="female">Female</option>
                        <option value="other">Others</option>
                        <option value="prefer not to say"
                            >Prefer not to say</option
                        >
                    </select>
                </div>
                <div class="mb-2">
                    <input
                        type="date"
                        id="birth"
                        bind:value={info.date_of_birth}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Date of Birth*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="residency"
                        bind:value={info.period_of_residency}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Period of Residency*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="password"
                        id="password"
                        bind:value={password}
                        on:input={handlePasswordInput}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Password*"
                        required
                    />
                    {#if passwordError}
                        <p class="text-red-500 text-sm mt-1">{passwordError}</p>
                    {/if}
                </div>
            </div>
            <div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="middle_name"
                        bind:value={info.middle_name}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Middle Name"
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="suffix"
                        bind:value={info.name_suffix}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Suffix"
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="email"
                        bind:value={email}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Email*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="address"
                        bind:value={info.address}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Address*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="text"
                        id="contact"
                        bind:value={info.phone_number}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Phone Number*"
                        required
                    />
                </div>
                <div class="mb-2">
                    <input
                        type="password"
                        id="password"
                        bind:value={confirm_password}
                        class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-96 p-2.5"
                        placeholder="Confirm Password*"
                        required
                    />
                </div>
            </div>
            <a href="/dashboard/admin/resident-db" class="text-white bg-green-400 hover:bg-green-500 focus:outline-none focus:ring-4 focus:ring-green-600 font-medium rounded-full text-sm px-5 py-2.5 text-center mb-2">
                <button>
                    Cancel
                </button>
            </a>
            
            <button
                id="create-account"
                type="submit"
                class="text-white bg-green-400 hover:bg-green-500 focus:outline-none focus:ring-4 focus:ring-green-600 font-medium rounded-full text-sm px-5 py-2.5 text-center ml-16 mb-2"
                >Create Account</button
            >
        </form>
    </div>
</div>
